# 惯用的 Python 适配器模式

> 原文：<https://dev.to/hmartinezdev/idiomatic-python-adapter-pattern-4mjc>

联合内容！检查原始的[惯用 Python - Adapter 模式](https://hectormartinez.dev/posts/adapter-pattern/)。

编写惯用的 Python(也称为*Python*代码)是一项非常受欢迎的技能，因为它提高了代码的可读性和可维护性。使用从 C++移植而来的匿名 Pythonic，我将向您介绍如何使用适配器设计模式来修改它。

## 非 Pythonic API

RDKit 是一个广泛使用的处理分子的工具包。有了它，你可以计算属性、构建分子、分解分子等等。核心数据结构和算法用 C++编写，Python 包装器使用 Boost 自动构建。所以它的 API 有 c++风格。下面是 Python 中的 RDKit 代码示例:

```
from rdkit.Chem import MolFromSmiles

celecoxib_smiles = "CC1=CC=C(C=C1)C1=CC(=NN1C1=CC=C(C=C1)S(N)(=O)=O)C(F)(F)F"
celecoxib_mol = MolFromSmiles(celecoxib_smiles)

print("This is the number of atoms that our molecule has:", celecoxib_mol.GetNumAtoms())
print("The symbols of its atoms: ", end="")
for atom in celecoxib_mol.GetAtoms(): # Note this line
    print(atom.GetSymbol(), end="")
print()

print("The atom with the index 0 has the symbol:", celecoxib_mol.GetAtomWithIdx(0).GetSymbol()) # Note this line print("Our molecule has the following properties:", celecoxib_mol.GetPropsAsDict()) # Note this line celecoxib_mol.SetBoolProp("Inspected", True)
print("Our molecule has the following properties:", celecoxib_mol.GetPropsAsDict()) 
```

```
$ python adapter-pattern-01.py
This is the number of atoms that our molecule has: 26
The symbols of its atoms: CCCCCCCCCCNNCCCCCCSNOOCFFF
The atom with the index 0 has the symbol: C
Our molecule has the following properties: {}
Our molecule has the following properties: {'Inspected': True} 
```

注意到`GetAtoms()`、`SetBoolProp()`还是`GetAtomWithIdx()`？在 Python 有更好的方法来做这件事。

## 适配器模式

对于 Python，我们希望有一个更 Python 化的 API，将 Python 习惯用法和语法转换成 RDKit API。为此，我们可以使用适配器模式。它会将当前的 RDKit 类包装到*适配器类*中，后者将调用 RDKit 对象方法。

我首先创建一个`MolAdapter`。它将一个字符串作为参数(微笑),并将 RDKit 分子存储在一个属性中。然后我定义了第一个*特殊方法* : `__len__`，当`len()`用在对象上时被调用。

```
from rdkit.Chem import MolFromSmiles

class MolAdapter(object):

    def __init__ (self, smiles):
        self.old_mol = MolFromSmiles(smiles)

    def __len__ (self):
        return self.old_mol.GetNumAtoms()

celecoxib_smiles = "CC1=CC=C(C=C1)C1=CC(=NN1C1=CC=C(C=C1)S(N)(=O)=O)C(F)(F)F"
celecoxib_mol = MolAdapter(celecoxib_smiles)
print("This is the number of atoms that our molecule has:", len(celecoxib_mol)) 
```

```
$ python adapter-pattern-02.py
This is the number of atoms that our molecule has: 26 
```

太好了！现在我可以在分子适配器上检索调用`len()`的原子数量。

## 更进一步

现在我修改了第一段代码中显示的行为。

```
from rdkit.Chem import MolFromSmiles

class MolPropertyTypeError(TypeError):
    pass

class MolAtomIndexError(IndexError):
    pass

class MolProperties(object):

    def __init__ (self, mol):
        self.mol = mol

    def __setitem__ (self, name, value):
        if isinstance(value, bool):
            self.mol.SetBoolProp(name, value)
        elif isinstance(value, int):
            self.mol.SetIntProp(name, value)
        elif isinstance(value, float):
            self.mol.SetDoubleProp(name, value)
        elif isinstance(value, str):
            self.mol.SetProp(name, value)
        else:
            err = f"Expected types: 'int', 'float', 'str' or 'bool', got '{type(value)}'."
            raise MolPropertyTypeError(err)

    def __getitem__ (self, name):
        return self.mol.GetProp(name)

    def __str__ (self):
        props = self.mol.GetPropsAsDict()
        items = ", ".join([f"{item}={value}" for item, value in props.items()])
        return f"{self. __class__. __name__ }({items})"

class MolAdapter(object):

    def __init__ (self, smiles):
        self.old_mol = MolFromSmiles(smiles)
        self.properties = MolProperties(self.old_mol)

    def __len__ (self):
        return self.old_mol.GetNumAtoms()

    def __getitem__ (self, index):
        if index > len(self):
            raise MolAtomIndexError(f"Atom with index {index} doesn't exists.")
        return self.old_mol.GetAtomWithIdx(index)

    def __iter__ (self):
        for atom in self.old_mol.GetAtoms():
            yield atom

celecoxib_smiles = "CC1=CC=C(C=C1)C1=CC(=NN1C1=CC=C(C=C1)S(N)(=O)=O)C(F)(F)F"
celecoxib_mol = MolAdapter(celecoxib_smiles)

print("This is the number of atoms that our molecule has:", len(celecoxib_mol))
print("The symbols of its atoms: ", end="")
for atom in celecoxib_mol:
    print(atom.GetSymbol(), end="")
print()

print("The atom with the index 0 has the symbol:", celecoxib_mol[0].GetSymbol())
print("Our molecule has the following properties:", celecoxib_mol.properties)
celecoxib_mol.properties["Inspected"] = True
print("Our molecule has the following properties:", celecoxib_mol.properties) 
```

```
$ python adapter-pattern-03.py
This is the number of atoms that our molecule has: 26
The symbols of its atoms: CCCCCCCCCCNNCCCCCCSNOOCFFF
The atom with the index 0 has the symbol: C
Our molecule has the following properties: MolProperties()
Our molecule has the following properties: MolProperties(Inspected=True) 
```

注意，我在这里实现了两个类:`MolProperties`和`MolAdapter`。我构建第一个是因为我已经使用了`__*item__`来将适配器视为可迭代的，所以我不能使用`celecoxib_mol["Inspected"]`来设置和检索属性:这将试图获得一个带有索引“Inspected”的原子。

对于`MolProperties`,我使用带有`__*item__`方法的自定义对象，而不是字典，因为我希望它将这些属性推送到`self.old_mol`本身，而不是保留在字典中。在`MolAdapter`上，我添加了`__getitem__`来索引原子，并添加了`__iter__`来使分子在原子上可迭代。我还创建了两个自定义异常来处理用户向属性传递非预期类型的情况，以及用户试图通过索引检索不存在的原子的情况。

## 外卖

*   您可以用适配器包装非 Pythonic API，这将为它们提供 Pythonic API
*   您可以定制*python 化的*动作(迭代一个对象，通过索引访问某个对象)等等。使用*特殊方法*如`__len__`、`__getiitem__`、`__setitem__`、`__iter__` …

## 进一步阅读

*   [Python 对象模型(特殊方法)](https://docs.python.org/3/reference/datamodel.html)
*   [RDKit](http://www.rdkit.org/)
*   [适配器设计模式](https://sourcemaking.com/design_patterns/adapter)
*   [Raymond Hettinger 适配器示例](https://youtu.be/wf-BqAjZb8M?t=12m43s)