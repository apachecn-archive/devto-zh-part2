# 角度测试备忘单

> 原文：<https://dev.to/lysofdev/an-angular-testing-cheatsheet-5hj2>

## 个人笔记

这是我为客户准备的内部文件的修订版。它基于最新的修订版，与客户的版本不同。

# 角度单元测试备忘单

下面是常见角度测试场景的代码示例的快速参考，以及一些改进我们测试实践的技巧。记得先测试！

### 测试场景

*   [隔离逻辑](#isolating-logic)
*   [异步行为](#async-behavior)
*   [间谍和模仿者](#spies-and-mocks)
*   [用户输入事件](#user-input-events)
*   [继承的功能](#inherited-functionality)
*   [应用事件](#application-events)
*   [服务](#services)
*   [输入变量](#input-variables)
*   [输出变量](#output-variables)
*   [生命周期方法](#life-cycle-methods)
*   [模拟方法链](#mock-method-chains)
*   [HTTP 调用](#http-calls)

* * *

## 隔离逻辑

使用助手函数封装应用程序其余部分的逻辑。避免将逻辑放在生命周期方法和其他挂钩中。避免从 helper 方法中引用组件的状态，尽管它是可用的。这将使单独测试变得更加容易。

#### 不好

```
ngOnInit() {
    ...
    this.clientPhoneNumbers = this.allClients
        .filter( ( client: Client ) => client.phone !== undefined && client.phone !== null )
        .map( ( client: Client ) => ( { name: client.name, phone: client.phone } ) );
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码示例很难测试。我们在`ngOnInit`方法中提供和/或模拟了每个操作的每个依赖项，只测试了三行代码。

#### 更好

```
ngOnInit() {
    ...
    this.collectClientPhoneNumbers();
    ...
}

collectClientPhoneNumbers() {
    this.clientPhoneNumbers = this.allClients
        .filter( ( client: Client ) => client.phone !== undefined && client.phone !== null )
        .map( ( client: Client ) => ( { name: client.name, phone: client.phone } ) );
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们改进的例子中，我们不再需要确保`ngOnInit`中的所有其他操作都成功，因为我们只测试了`collectClientPhoneNumbers`方法。然而，我们仍然必须为 allClients 字段模拟或提供组件的状态。

#### 最好

```
ngOnInit() {
    ...
    this.clientPhoneNumbers = this.collectClientPhoneNumbers( this.allClients );
    ...
}

collectClientPhoneNumbers( clients: Client[] ): Object[] {
    return clients
        .filter( ( client: Client ) => client.phone !== undefined && client.phone !== null )
        .map( ( client: Client ) => ( { name: client.name, phone: client.phone } ) );
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们的最佳实现中，逻辑完全独立于组件的状态。如果我们的组件可以编译，我们不需要模仿任何东西，只需要提供普通的 JS 输入。

#### 测试示例

```
it( 'When collectClientPhoneNumbers receives a list of Clients, then it should return a list of phone numbers', () => {

    // GIVEN - Load test data and define expected results.
    const clients = loadFromMockData('valid-clients');
    const firstClientPhoneNumber = { name: client[0].name, phone: client[0].number };
    const clientsWithPhoneNumbers = clients.filter( c => client.phone !== undefined && client.phone !== null );

    // WHEN - Perform the operation and capture results.
    const filteredClients = component.collectClientPhoneNumbers( clients );

    // THEN - Compare results with expected values.
    expect( filteredClients.length ).toEqual( clientsWithPhoneNumbers.length );
    expect( filteredClients[0] ).toEqual( firstClientPhoneNumber );

} ); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

# 异步行为

角度测试模块提供了两个用于测试异步操作的实用程序。

#### 异步测试工具说明

*   **async** :测试将一直等到所有的异步行为都解决后才结束。最好测试简单的异步行为，不要阻塞太长时间。避免使用可能在解析前挂起或持续很长时间的 with async 行为。
*   **fakeAsync** :测试将拦截异步行为并同步执行。最适合测试可能挂起或需要很长时间才能解决的异步行为链或不可靠的异步行为。
*   **勾选**:在 *fakeAsync* 测试中模拟时间的流逝。需要一个以毫秒为单位表示运行时间的数值参数。
*   **刷新微任务**:强制完成所有未完成的**微任务**，如*承诺*和*可观察*。
*   **flush** :强制完成所有未完成的**宏任务**，如 *setInterval* 、 *setTimeout* 等。####要测试的代码

```
class SlowService {

    names: BehaviorSubject<string[]> = new BehaviorSubject<>( [] );

    getNames(): Observable<string[]> {
        return this.names;
    }

    updateNames( names: string[] ) {
        setTimeout( () => this.names.next( names ), 3000 );
    }

}

class SlowComponent implements OnInit {

    names: string[];

    constructor( private slowService: SlowService ) {}

    ngOnInit() {
        this.slowService.getNames().subscribe( ( names: string[] ) => {
            this.names = names;
        } );
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

#### 测试示例`async()`

```
it( 'When updatedNames is passed a list of names, Then the subscription will update with a list of names', async( 
    inject( [SlowService], ( slowService ) => {

    // GIVEN - Create test data, initialize component and assert component's initial state
    const names = [ "Bob", "Mark" ];
    component.ngOnInit();
    fixture.whenStable()
    .then( () => {
        expect( component.names ).toBeDefined();
        expect( component.names.length ).toEqual( 0 );

        // WHEN - Simulate an update in the service's state and wait for asynchronous operations to complete
        slowService.updateNames( names );
        return fixture.whenStable();
    } )
    .then( () => {

        // THEN - Assert changes in component's state
        expect( component.names.length ).toEqual( 2 );
        expect( component.names ).toEqual( names );
    } );

} ) ) ); 
```

Enter fullscreen mode Exit fullscreen mode

#### 测试示例`fakeAsync()`、`tick()`、`flush()`、`flushMicrotasks()`

```
it( 'When updatedNames is passed a list of names, Then the subscription will update with a list of names', fakeAsync( 
    inject( [SlowService], ( slowService ) => {

    // GIVEN - Create test data, initialize component and assert component's initial state
    const names = [ "Bob", "Mark" ];
    component.ngOnInit();
    flushMicrotasks();
    expect( component.names ).toBeDefined();
    expect( component.names.length ).toEqual( 0 );

    // WHEN - Simulate an update in the service's state and wait for asynchronous operations to complete
    slowService.updateNames( names );
    tick( 3001 );

    // THEN - Assert changes in component's state
    expect( component.names.length ).toEqual( 2 );
    expect( component.names ).toEqual( names );

} ) ) ); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 间谍和嘲笑

监视功能允许我们验证组件之间的交互在正确的条件下发生。我们使用模拟对象来减少被测试的代码量。Jasmine 提供了`spyOn()`功能，让我们管理间谍和模仿。

#### 案例 1:断言调用了一个方法。

```
const obj = { method: () => null };
spyOn( obj, 'method' );
obj.method();
expect( obj.method ).toHaveBeenCalled(); 
```

Enter fullscreen mode Exit fullscreen mode

**警告**:监视一个方法将会阻止该方法被实际执行。

#### 案例二:断言一个方法被调用，执行方法。

```
const obj = { getName: () => 'Sam' };
spyOn( obj, 'getName' ).and.callThrough();
expect( obj.getName() ).toEqual( 'Sam' );
expect( obj.getName ).toHaveBeenCalled(); 
```

Enter fullscreen mode Exit fullscreen mode

#### 案例 3:断言一个方法被调用，执行一个函数。

```
const obj = { getName: () => 'Sam' };
spyOn( obj, 'getName' ).and.callFake((args) => console.log(args));
expect( obj.getName() ).toEqual( 'Sam' );
expect( obj.getName ).toHaveBeenCalled(); 
```

Enter fullscreen mode Exit fullscreen mode

#### 案例 4:模拟一个现有方法的响应。

```
const obj = { mustBeTrue: () => false };
spyOn( obj, 'mustBeTrue' ).and.returnValue( true );
expect( obj.mustBeTrue() ).toBe( true ); 
```

Enter fullscreen mode Exit fullscreen mode

#### 案例 5:对一个已有方法模拟几个响应。

```
const iterator = { next: () => null };
spyOn( iterator, 'next' ).and.returnValues( 1, 2 );
expect( iterator.next ).toEqual( 1 );
expect( iterator.next ).toEqual( 2 ); 
```

Enter fullscreen mode Exit fullscreen mode

#### 案例 6:断言一个方法被调用了不止一次。

```
const obj = { method: () => null };
spyOn( obj, 'method' );
for ( let i = 0; i < 3; i++ {
    obj.method();
}
expect( obj.method ).toHaveBeenCalledTimes( 3 ); 
```

Enter fullscreen mode Exit fullscreen mode

#### 案例 7:断言一个方法被带参数调用

```
const calculator = { add: ( x: number, y: number ) => x + y };
spyOn( calculator, 'add' ).and.callThrough();
expect( calculator.add( 3, 4 ) ).toEqual( 7 );
expect( calculator.add ).toHaveBeenCalledWith( 3, 4 ); 
```

Enter fullscreen mode Exit fullscreen mode

#### 案例 8:断言一个方法被带参数调用了几次

```
const ids = [ 'ABC123', 'DEF456' ];
const db = { store: ( id: string) => void };
spyOn( db, 'store' );
ids.forEach( ( id: string ) => db.store( id ) );
expect( db.store ).toHaveBeenCalledWith( 'ABC123' );
expect( db.store ).toHaveBeenCalledWith( 'DEF456' ); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 用户输入事件

我们可以通过模拟`DebugElement`上的事件来模拟用户输入，而不必与 DOM 交互。`DebugElement`是作为`HTMLElement`的角度组件的浏览器无关渲染。这意味着我们可以在没有浏览器的情况下测试元素来呈现实际的 HTML。

#### 要测试的组件

```
@Component({
    selector: 'simple-button',
    template: `
        <div class="unnecessary-container">
            <button (click)="increment()">Click Me!</button>
        </div>
    `
})
class SimpleButtonComponent {

    clickCounter: number = 0;

    increment() {
        this.clickCounter += 1;
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

#### 测试示例

```
it( 'When the button is clicked, then click counter should increment', () => {

    // GIVEN - Capture reference to DebugElement not NativeElement and verify initial state
    const buttonDE = fixture.debugElement.find( By.css( 'button' ) );
    expect( component.clickCounter ).toEqual( 0 );

    // WHEN - Simulate the user input event and detect changes.
    buttonDE.triggerEventHandler( 'click', {} );
    fixture.detectChanges();

    // THEN - Assert change in component's state
    expect( component.clickCounter ).toEqual( 1 );

} ); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 继承功能

我们不应该测试父类继承子类的功能。相反，这种继承的功能应该被嘲笑。

#### 父类

```
class ResourceComponent {

    protected getAllResources( resourceName ): Resource[] {
        return this.externalSource.get( resourceName );
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

#### 子类

```
class ContactsComponent extends ResourceComponent {

    getAvailableContacts(): Contact[] {
        return this.getAllResources( 'contacts' )
            .filter( ( contact: Contact ) => contact.available );
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

#### 测试示例

```
it( 'When the getAvailableContacts method is called, Then it should return contacts where available is true', () => {

    // GIVEN - Intercept call to inherited method and return a mocked response.
    spyOn( component, 'getAllResources' ).and.returnValue( [ 
        { id: 1, name: 'Charles McGill', available: false },
        { id: 2, name: 'Tom Tso', available: true },
        { id: 3, name: 'Ruben Blades', available: true }
    ] );

    // WHEN - Perform operation on inheriting class
    const contacts = component.getAvailableContacts();

    // THEN - Assert that interaction between inherited and inheriting is correctly applied.
    expect( component.getAllResources ).toHaveBeenCalledWith( 'contacts' );
    expect( contacts.length ).toEqual( 2 );
    expect( contacts.any( c => name === 'Charles McGill' ) ).toBe( false );

} ); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 服务

使用`inject()`函数测试服务对象。`TestBed`将为每个测试注入一个服务对象的新实例。在测试异步行为时使用`async()`函数，比如 Observables 和 Promises。使用`of()`模拟可观察到的东西。

#### 代码测试

```
class NameService {

    constructor( private cache: CacheService ) {}

    getNames(): Observable<string[]> {
        return this.cache.get( 'names' );
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

#### 测试示例

```
it( 'When getNames is called Then return an observable list of strings', async( 
    inject( [CacheService, NameService], ( cache, nameService ) => {

    // GIVEN - Mock service dependencies with expected value
    const testNames = ["Raul", "Fareed", "Mark"];
    spyOn( cache, 'get' ).and.returnValue( of( testNames ) );

    // WHEN - Subscribe to observable returned by service method
    nameService.getNames().subscribe( ( names: string[] ) => {

        // THEN - Assert result matches expected value
        expect( names ).toMatch( testNames );

    } );

} ) ); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 输入变量

从角度 5 开始，组件输入的行为就像普通属性一样。我们可以使用夹具变化检测来测试变化。

#### 代码测试

```
class CounterComponent implements OnChanges {

    @Input() value: string;
    changeCounter: number = 0;

    ngOnChanges() {
        changeCounter++;
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

#### 测试示例

```
it( 'When the value input is changed, the changeCounter incrementsByOne', () => {

    // GIVEN - Spy on the ngOnChanges lifecycle method and assert initial state.
    spyOn( component, 'ngOnChanges' );
    expect( component.value ).toBeUndefined();
    expect( component.changeCouner ).toEqual( 0 );

    // WHEN - Set the input variable and call on fixture to detect changes.
    component.value = 'First Value';
    fixture.detectChanges();

    // THEN - Assert that lifecycle method was called and state has been updated.
    expect( component.ngOnChanges ).toHaveBeenCalled();
    expect( component.changeCounter ).toEqual( 1 );

} ); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 输出变量

组件通常将事件发射器作为输出变量公开。我们可以直接监视这些发射器，以避免测试异步订阅。

#### 代码测试

```
class EmittingComponent {

    @Output() valueUpdated: EventEmitter<string> = new EventEmitter<>();

    updateValue( value: string ) {
        this.valueUpdated.emit( value );
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

#### 测试示例

```
it( 'When the updateValue() method is called with a string, Then the valueUpdated output will emit with the string', () => {

    // GIVEN - Create a test argument and spy on the emitting output variable.
    const value = 'Test Value';
    spyOn( component.valueUpdated, 'emit' );

    // WHEN - Call a method that will trigger the output variable to emit.
    component.updateValue( value );

    // THEN - Assert that the output variable has emitted correctly with the test argument.
    expect( component.valueUpdated.emit ).toHaveBeenCalledWith( value );

} ); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 应用事件

测试由全局对象或父组件触发的事件可以通过在 fakeAsync 环境中模拟事件调度来完成。我们可以使用`flush()`函数以同步方式解决所有未决的异步操作。

#### 代码测试

```
class ListeningComponent {

    focus: string;

    @HostListener( 'window:focus-on-dashboard', ['$event'] )
    onFocusOnDashboard() {
        this.focus = 'dashboard';
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

#### 测试示例

```
it( 'When the window dispatches a focus-on-dashboard event, Then the focus is set to dashboard', fakeAsync( () => {

    // GIVEN - Prepare spy for callback and validate initial state.
    spyOn( component, 'onFocusOnDashboard' );
    expect( component.focus ).not.toEqual( 'dashboard' );

    // WHEN - Dispatch the event, resolve all pending, asynchronous operations and call fixture to detect changes.
    window.dispatchEvent( new Event( 'focus-on-dashboard' ) );
    flush();
    fixture.detectChanges();

    // THEN - Assert that callback was called and state has changed correctly.
    expect( component.onFocusOnDashboard ).toHaveBeenCalled();
    expect( component.focus ).toEqual( 'dashboard' );

} ) ); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 生命周期方法

没有真正的理由去测试一个生命周期方法。这将是对框架的测试，超出了我们的职责范围。生命周期方法所需的任何逻辑都应该封装在一个助手方法中。测试一下。关于需要调用`ngOnInit()`生命周期方法的测试，请参见[异步行为](#async-behavior)。

* * *

## 模拟方法链

我们有时可能需要模拟一系列方法链形式的方法调用。这可以通过使用`spyOn`功能来实现。

#### 代码测试

```
class DatabseService {

    db: DatabaseAdapter;

    getAdultUsers(): User[] {
        return this.db.get( 'users' ).filter( 'age > 17' ).sort( 'age', 'DESC' );
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

#### 测试示例

```
it( 'When getAdultUsers is called, Then return users above 17 years of age', inject([DatabaseService], ( databaseService ) => {

    // GIVEN - Mock the database adapter object and the chained methods
    const testUsers = [
        { id: 1, name: 'Bob Odenkirk' },
        { id: 2, name: 'Ralph Morty' }
    ];
    const db = { get: () => {}, filter: () => {}, sort: () => {} };
    spyOn( db, 'get' ).and.returnValue( db );
    spyOn( db, 'filter' ).and.returnValue( db );
    spyOn( db, 'sort' ).and.returnValue( testUsers );
    databaseService.db = db;

    // WHEN - Test the method call
    const users = databaseService.getAdultUsers();

    // THEN - Test interaction with method chain
    expect( db.get ).toHaveBeenCalledWith( 'users' );
    expect( db.filter ).toHaveBeenCalledWith( 'age > 17' );
    expect( db.sort ).toHaveBeenCalledWith( 'age', 'DESC' );
    expect( users ).toEqual( testUsers );

} ) ); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## HTTP 调用

Angular 提供了几个工具，用于在测试套件中拦截和模仿 http 调用。我们不应该在测试期间执行真正的 http 调用。几个重要的对象:

*   **xhr backed**:拦截 *HTTP* 或 *HTTPClient* 执行的请求。
*   **MockBackend** :测试 API，用于配置 XHRBackend 将如何与拦截的请求进行交互。
*   **MockConnection** :测试 API，用于配置单独的、被拦截的请求和响应。

#### 代码测试

```
class SearchService {

    private url: string = 'http://localhost:3000/search?query=';

    constructor( private http: Http ) {}

    search( query: string ): Observable<string[]> {
        return this.http.get( this.url + query, { withCredentials: true } ).pipe(
            catchError( ( error: any ) => {
                UtilService.logError( error );
                return of( [] );
            } )
        );
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

#### 文本示例

```
let backend: MockBackend;
let lastConnection: MockConnection;

beforeEach( () => {

    TestBed.configureTestingModule( {
        imports: [HttpModule],
        providers: [
            { provide: XHRBackend, useClass: MockBackend },
            SearchService
        ]
    } );

    backend = TestBed.get(XHRBackend) as MockBackend;
    backend.connections.subscribe( ( connection: MockConnection ) => {
        lastConnection = connection;
    } );

} );

it( 'When a search request is sent, Then receive an array of string search results.', 
    fakeAsync( inject( [SearchService], ( searchService: SearchService ) => {

        // GIVEN - Prepare mock search results in the form of a HTTP Response
        const expectedSearchResults = [ ... ];
        const mockJSON = JSON.stringify( { data: expectedSearchResults } );
        const mockBody = new ResponseOptions( { body: mockJSON } );
        const mockResponse = new Response( mockBody );

        // WHEN - Perform the call and intercept the connection with a mock response.
        let receivedSearchResults: string[];   
        searchService.search( 'reso' ).subscribe( ( searchResults: string[] ) => {
            receivedSearchResults = searchResults;
        } );
        lastConnection.mockRespond( mockResponse );

        // THEN - Complete the pending transaction and assert that the mock response
        // was received and processed correctly.
        flushMicrotasks();
        expect( receivedSearchResults ).toBeDefined();
        expect( receivedSearchResults ).toEqual( expectedSearchResults );
    } ) )
); 
```

Enter fullscreen mode Exit fullscreen mode