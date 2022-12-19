# 一个星形(A*)路径查找 C++

> 原文：<https://dev.to/jansonsa/a-star-a-path-finding-c-4a4h>

A* (A star)寻路算法是著名的 Dijkstra 寻路算法的扩展，效率更高，但偶尔实际上并不会找到**最佳**路线，而只是一条**足够好的**路线。

[![A Star](../Images/2b15431bacac85cf3a367bd85a42c56d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n311fF3r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5t0o6cjblypt3g990xl9.PNG)

### a*算法实现

让我们考虑一下，我们的地图是由 *1000x500* 个像素划分成 *50x25* 个块。我正在使用*的神奇数字*让它尽可能广泛地被使用。

```
#define X_MAX 1000
#define X_STEP 20
#define Y_MAX 500
#define Y_STEP 20 
```

然后我制作一个新的*结构*来表示地图上的每个节点(方块)。因为稍后我将把这个*结构*放到一个*集合*中，我首先需要重载操作符 *<* 来告诉*集合*我们比较哪个值，因为它是一个有序序列。

```
struct Node
{
    int y;
    int x;
    int parentX;
    int parentY;
    float gCost;
    float hCost; 
    float fCost;
};

inline bool operator < (const Node& lhs, const Node& rhs)
{//We need to overload "<" to put our struct into a set
    return lhs.fCost < rhs.fCost;
} 
```

##### 然后我们让*成为我们的类*，在里面我们使用这些函数:

首先我们需要一个布尔值来检查一个**节点**(正方形)是否有效——不是一个障碍，也不是越界。

```
 static bool isValid(int x, int y) { //If our Node is an obstacle it is not valid
        int id = x + y * (X_MAX / X_STEP);
        if (world.obstacles.count(id) == 0) {
            if (x < 0 || y < 0 || x >= (X_MAX / X_STEP) || y >= (Y_MAX / Y_STEP)) {
                return false;
            }
            return true;
        } 
        return false;
    } 
```

我们还需要一个布尔值来检查一个**节点**是否是我们的目的地。

```
 static bool isDestination(int x, int y, Node dest) {
        if (x == dest.x && y == dest.y) {
            return true;
        }
        return false;
    } 
```

这是它开始不同于 Dijkstra 的地方。我们使用 H 成本和 F 成本，所以我们首先检查向目的地移动的节点，然后才检查远离目的地的节点。

```
 static double calculateH(int x, int y, Node dest) {
        double H = (sqrt((x - dest.x)*(x - dest.x)
            + (y - dest.y)*(y - dest.y)));
        return H;
    } 
```

#### 现在算法本身

```
 static vector<Node> aStar(Node player, Node dest) {
        vector<Node> empty;
        if (isValid(dest.x, dest.y) == false) {
            cout << "Destination is an obstacle" << endl;
            return empty;
            //Destination is invalid
        }
        if (isDestination(player.x, player.y, dest)) {
            cout << "You are the destination" << endl;
            return empty;
            //You clicked on yourself
        }
        bool closedList[(X_MAX / X_STEP)][(Y_MAX / Y_STEP)];

        //Initialize whole map
        //Node allMap[50][25];
        array<array < Node, (Y_MAX / Y_STEP)>, (X_MAX / X_STEP)> allMap;
        for (int x = 0; x < (X_MAX / X_STEP); x++) {
            for (int y = 0; y < (Y_MAX / Y_STEP); y++) {
                allMap[x][y].fCost = FLT_MAX;
                allMap[x][y].gCost = FLT_MAX;
                allMap[x][y].hCost = FLT_MAX;
                allMap[x][y].parentX = -1;
                allMap[x][y].parentY = -1;
                allMap[x][y].x = x;
                allMap[x][y].y = y;

                closedList[x][y] = false;
            }
        }

        //Initialize our starting list
        int x = player.x;
        int y = player.y;
        allMap[x][y].fCost = 0.0;
        allMap[x][y].gCost = 0.0;
        allMap[x][y].hCost = 0.0;
        allMap[x][y].parentX = x;
        allMap[x][y].parentY = y;

        vector<Node> openList;  
        openList.emplace_back(allMap[x][y]);
        bool destinationFound = false;

        while (!openList.empty()&&openList.size()<(X_MAX / X_STEP)*(Y_MAX / Y_STEP)) {
            Node node;
            do {
                //This do-while loop could be replaced with extracting the first
                //element from a set, but you'd have to make the openList a set.
                //To be completely honest, I don't remember the reason why I do
                //it with a vector, but for now it's still an option, although
                //not as good as a set performance wise.
                float temp = FLT_MAX;
                vector<Node>::iterator itNode;
                for (vector<Node>::iterator it = openList.begin();
                    it != openList.end(); it = next(it)) {
                    Node n = *it;
                    if (n.fCost < temp) {
                        temp = n.fCost;
                        itNode = it;
                    }
                }
                node = *itNode;
                openList.erase(itNode);
            } while (isValid(node.x, node.y) == false);

            x = node.x;
            y = node.y;
            closedList[x][y] = true;

            //For each neighbour starting from North-West to South-East
            for (int newX = -1; newX <= 1; newX++) {
                for (int newY = -1; newY <= 1; newY++) {
                    double gNew, hNew, fNew;
                    if (isValid(x + newX, y + newY)) {
                        if (isDestination(x + newX, y + newY, dest))
                        {
                            //Destination found - make path
                            allMap[x + newX][y + newY].parentX = x;
                            allMap[x + newX][y + newY].parentY = y;
                            destinationFound = true;
                            return makePath(allMap, dest);
                        }
                        else if (closedList[x + newX][y + newY] == false)
                        {
                            gNew = node.gCost + 1.0;
                            hNew = calculateH(x + newX, y + newY, dest);
                            fNew = gNew + hNew;
                            // Check if this path is better than the one already present
                            if (allMap[x + newX][y + newY].fCost == FLT_MAX ||
                                allMap[x + newX][y + newY].fCost > fNew)
                            {
                                // Update the details of this neighbour node
                                allMap[x + newX][y + newY].fCost = fNew;
                                allMap[x + newX][y + newY].gCost = gNew;
                                allMap[x + newX][y + newY].hCost = hNew;
                                allMap[x + newX][y + newY].parentX = x;
                                allMap[x + newX][y + newY].parentY = y;
                                openList.emplace_back(allMap[x + newX][y + newY]);
                            }
                        }
                    }
                }
            }
            }
            if (destinationFound == false) {
                cout << "Destination not found" << endl;
                return empty;
        }
    } 
```

你可能会注意到我们仍然缺少一个函数 *makePath* 。这就是我们跟随每个父节点，从我们的目的节点开始，直到我们到达我们的起点，同时把它们放在一个向量中，我们稍后将传递到 *aStar* ，从那里到任何我们可能会发现有用的地方。

```
 static vector<Node> makePath(array<array<Node, (Y_MAX / Y_STEP)>, (X_MAX / X_STEP)> map, Node dest) {
        try {
            cout << "Found a path" << endl;
            int x = dest.x;
            int y = dest.y;
            stack<Node> path;
            vector<Node> usablePath;

            while (!(map[x][y].parentX == x && map[x][y].parentY == y)
                && map[x][y].x != -1 && map[x][y].y != -1) 
            {
                path.push(map[x][y]);
                int tempX = map[x][y].parentX;
                int tempY = map[x][y].parentY;
                x = tempX;
                y = tempY;

            }
            path.push(map[x][y]);

            while (!path.empty()) {
                Node top = path.top();
                path.pop();
                usablePath.emplace_back(top);
            }
            return usablePath;
        }
        catch(const exception& e){
            cout << e.what() << endl;
        }
    } 
```

#### 实际使用的算法:

首先，我们为起始位置创建一个**节点**，为目的位置创建一个节点，然后运行算法。

```
 Node player;
            player.x = X / X_STEP;
            player.y = Y / Y_STEP;

            Node destination;
            destination.x = e->X / X_STEP;
            destination.y = e->Y / Y_STEP;

            for (Node node : YourClass::aStar(player, destination)) {
                //Your code here

            } 
```

查看我的口袋妖怪游戏，我在这里使用这个算法[。](https://dev.to/jansonsa/pokmon-or-not-pokmon-5dn6)
并在这里查看刚才的代码[。](https://github.coventry.ac.uk/jansonsa/D1/blob/master/pokemonGUI/pokemonGUI/Cordinate.h)