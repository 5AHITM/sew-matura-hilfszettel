# sew-matura-hilfszettel

# Angular

## Bindings

```html
<p>{{ text }}</p>
<button [disabled]="text == 'Hello World!'" (click)="handleClick()">btn</button>
<input [(ngModel)]="name" />
```

```tsx
export class AppComponent {
  text = "Welcome to demo1";
  name = "unknown";

  handleClick() {
    this.text = "Hello World!";
  }
}
```

## Direktiven

```html
<p *ngIf="name !== ''">Hello {{ name }}</p>
<p [ngStyle]="color: getColor()" [ngClass]="type: getTextType()"></p>
<ul>
  <li *ngFor="let user of users; let i = index">{{ i }} - {{ user }}</li>
</ul>

<div [ngSwitch]="day">
  <p *ngSwitchCase="days.MONDAY">Montag</p>
  <p *ngSwitchCase="days.TUESDAY">Dienstag</p>
  <p *ngSwitchDefault>Sonstiger Tag</p>
</div>
```

```tsx
export class AppComponent {
  name = "unknown";
  color = "red";
  type = "bold";
  users = ["user1", "user2", "user3"];
  day = Days.MONDAY;
  days = Days;

  getColor() {
    return this.color;
  }

  getTextType() {
    return this.type;
  }
}

export enum Days {
  MONDAY,
  TUESDAY,
  WEDNESDAY,
}
```

## Components Databinding

```tsx
@Input('firstname') firstname!: string;
@Input('lastname') lastname!: string;
@Output() removeEvent = new EventEmitter();
@ViewChild('nicknameInput', {static: false}) nicknameInput: ElementRef;
nickname = '';

login() {
  this.nickname = this.nicknameInput.nativeElement.value;
}

remove() {
this.removeEvent.emit();
}
```

```html
<input type="text" #nicknameInput />
<button (click)="login()">Login</button>

<p>Hello {{nickname}}</p>

<p #pTag>gets removed</p>
<some-component
  [firstname]="'Max'"
  lastname="Muster"
  (removeEvent)="handleRemoveEvent(pTag)"
></some-component>
```

```tsx
handleRemoveEvent(pTag: HTMLElement) {
console.log(pTag.innerHTML);
}
```

## Services

```tsx
export class LoggingService() {
	public logStatus(status: string) {
		console.log(status);
	}
}
```

```tsx
constructor(private service: LoggingService) {}

onStatusChange(status: string) {
	this.service.logStatus(status);
}
```

## Routing

### Konfiguration

```tsx
const appRoutes: Routes = [
	{ path: '', component: HomeComponent },
	{ path: 'gallery', component: GalleryComponent },
	{ path: 'gallery/:index', component: DetailComponent},
];

...
imports: [
	BrowserModule,
	FormsModule,
	RouterModule.forRoot(appRoutes)
]
...
```

### Verwendung

```html
<a routerLink="/gallery">Gallery</a>
<a routerLink="/gallery" [queryParams]="{category: 'Nature'}">
  Gallery for Nature Pictures
</a>
<router-outlet></router-outlet>
```

```tsx
constructor(private router: Router, private route: ActivatedRoute) {}

this.router.navigate(['gallery']);
this.router.navigate(['gallery'], {relativeTo: this.route});
this.router.navigate(['gallery', index]);
this.router.navigate(['gallery'], {queryParams: {category: 'Nature'}});
```

```tsx
export class TextComponent implements OnInit {
  index: string;
  category: string;

  constructor(private route: ActivatedRoute) {}

  ngOnInit() {
    this.route.snapshot.params.subscribe((params: Params) => {
      this.index = params["index"];
    });
    this.route.snapshot.queryParams.subscribe((params: Params) => {
      this.category = params["category"];
    });
  }
}
```

## Observable

```tsx
export class AppComponent implements OnInit, OnDestroy {
  myObsSubscription: Subscription;

  ngOnInit() {
    const myObservable = Observable.create((observer: Observer<string>) => {
      setTimeout(() => {
        observer.next("first package");
      }, 2000);
      setTimeout(() => {
        observer.next("second package");
      }, 4000);
      setTimeout(() => {
        observer.error("this does not work");
      }, 5000);
    });

    this.myObsSubscription = myObservable.subscribe(
      (data: string) => {
        console.log(data);
      },
      (error: string) => {
        console.log(error);
      },
      () => {
        console.log("completed");
      }
    );
  }

  ngOnDestroy() {
    this.myObsSubscription.unsubscribe();
  }
}
```

### Subject

```tsx
export class SearchService {
  searchSubject = new Subject<string>();

  constructor() {}

  search(term: string) {
    this.searchSubject.next(term);
  }
}
```

```tsx
export class Comp1Component implements OnInit {
  constructor(private searchService: SearchService) {}

  ngOnInit() {}

  search(term: HTMLInputElement) {
    this.searchService.search(term.value);
  }
}
```

```tsx
export class Comp2Component implements OnInit {
  searchSubscription: Subscription;

  constructor(private searchService: SearchService) {}

  ngOnInit() {
    this.searchSubscription = this.searchService.searchSubject.subscribe(
      (data: string) => {
        console.log(data);
      }
    );
  }

  ngOnDestroy() {
    this.searchSubscription.unsubscribe();
  }
}
```

## Forms

### Template Driven

```html
<form (ngSubmit)="onSubmit(f)" #f="ngForm">
  <div class="form-group">
    <label for="username">Username</label>
    <input
      type="text"
      id="username"
      class="form-control"
      name="username"
      ngModel
      required
    />
  </div>
  <div class="form-group">
    <label for="email">Email</label>
    <input
      type="email"
      id="email"
      class="form-control"
      name="email"
      ngModel
      required
      email
      #email="ngModel"
    />
    <span class="help-block" *ngIf="!email.valid && email.touched">
      Please enter a valid email address!
    </span>
  </div>

  <button type="submit" class="btn btn-primary" [disabled]="!f.valid">
    Submit
  </button>
</form>
```

```tsx
export class AppComponent {
  onSubmit(form: NgForm) {
    console.log(form.value.username);
  }
}
```

### Reactive

```html
<form [formGroup]="regForm">
  <div class="form-group">
    <label for="username">Username</label>
    <input
      type="text"
      id="username"
      class="form-control"
      formControlName="username"
    />
    <span
      *ngIf="!regForm.get('username').valid && regForm.get('username').touched"
      class="help-block"
    >
      Please enter a valid username!
    </span>
  </div>
  <div class="form-group">
    <label for="email">Email</label>
    <input
      type="email"
      id="email"
      class="form-control"
      formControlName="email"
    />
    <span
      *ngIf="!regForm.get('email').valid && regForm.get('email').touched"
      class="help-block"
    >
      Please enter a valid email address!
    </span>
  </div>

  <button
    type="submit"
    class="btn btn-primary"
    (click)="onSubmit()"
    [disabled]="!regForm.valid"
  >
    Submit
  </button>
</form>
```

```tsx
export class AppComponent implements OnInit {
  regForm: FormGroup;

  ngOnInit() {
    this.regForm = new FormGroup({
      username: new FormControl(null, Validators.required),
      email: new FormControl(null, [Validators.required, Validators.email]),
    });
  }

  onSubmit() {
    console.log(this.regForm.value);
  }
}
```

## HTTP

```tsx
 @NgModule({
  declarations: [AppComponent],
  imports: [
	BrowserModule,
	FormsModule,
	HttpClientModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
```

```tsx
export class PostService {
  constructor(private http: HttpClient) {}

  createAndStorePost(title: string, content: string) {
    const postData: Post = { title: title, content: content };
    this.http
      .post<{ name: string }>(
        "https://ng-complete-guide-6f4f5.firebaseio.com/posts.json",
        postData
      )
      .subscribe((responseData) => {
        console.log(responseData);
      });
  }

  fetchPosts() {
    this.http.get<Post>(
      "https://ng-complete-guide-6f4f5.firebaseio.com/posts.json"
    );
  }

  fetchPostsWithHeaders() {
    return this.http.get<Post>(
      "https://ng-complete-guide-6f4f5.firebaseio.com/posts.json",
      {
        headers: new HttpHeaders({ "Custom-Header": "Hello" }),
        params: new HttpParams().set("print", "pretty"),
        observe: "body", // oder 'response', 'events'
      }
    );
  }

  deletePosts() {
    return this.http.delete(
      "https://ng-complete-guide-6f4f5.firebaseio.com/posts.json"
    );
  }
}
```

### Usage

```tsx
export class AppComponent implements OnInit {
  loadedPosts: Post[] = [];
  error = null;

  constructor(private postService: PostService) {}

  ngOnInit() {
    this.postService.fetchPosts().subscribe(
      (posts) => {
        this.loadedPosts = posts;
      },
      (error) => {
        this.error = error.message;
        console.log(error);
      }
    );
  }

  onCreatePost(postData: Post) {
    this.postService.createAndStorePost(postData.title, postData.content);
  }

  onFetchPosts() {
    this.postService.fetchPosts();
  }

  onClearPosts() {
    this.postService.deletePosts().subscribe(() => {
      this.loadedPosts = [];
    });
  }
}
```

## Websockets

```tsx
export class WebSocketService implements NgOnInit {
  myWebSocket: WebSocketSubject<Message>;

  ngOnInit() {
    this.myWebSocket = webSocket("ws://localhost:8080/ws");
    this.myWebSocket.asObservable().subscribe(
      (msg: Message) => console.log("message received: " + msg),
      (err: Event) => console.log("error: " + err),
      () => console.log("complete")
    );
  }

  sendMessage(msg: Message) {
    this.myWebSocket.next(msg);
  }

  close() {
    this.myWebSocket.complete();
  }
}
```

# Quarkus

## Entities and ID Generation

### Autoincrement

```java
@Entity
public class Address {
  @Id
  @GeneratedValue()
  private Long id;

  private String street;
}
```

### Table

```java
@Entity
@TableGenerator(name = "addressGen", initialValue = 1000, allocationSize = 50)
public class Address {
  @Id
  @GeneratedValue(strategy = GenerationType.TABLE, generator = "addressGen")
  private Long id;

  private String street;
}
```

### Sequence

```java
@Entity
@SequenceGenerator(name = "addressSeq", initialValue = 1000, allocationSize = 50)
public class Address {
  @Id
  @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "addressSeq")
  private Long id;

  private String street;
}
```

### Embedded ID

```java
@Entity
public class Address {
  @EmbeddedId
  private AddressId id;

  private String street;
}
```

```java
@Embeddable
public class AddressId implements Serializable {
  private Long id;

  private String city;
}
```

## Entity Relations

### One to One

```java
@Entity
public class Address {
  @Id
  @GeneratedValue()
  private Long id;

  private String street;

  @OneToOne(mappedBy = "address")
  private Person person;
}
```

```java
@Entity
public class Person {
  @Id
  @GeneratedValue()
  private Long id;

  private String name;

  @OneToOne
  private Address address;
}
```

### One to Many

```java
@Entity
public class Address {
  @Id
  @GeneratedValue()
  private Long id;

  private String street;

  @OneToMany(mappedBy = "address", cascade = CascadeType.ALL)
  @JoinColumn(name = "address_id")
  private List<Person> persons;
}
```

```java
@Entity
public class Person {
  @Id
  @GeneratedValue()
  private Long id;

  private String name;

  @ManyToOne
  private Address address;
}
```

### Many to Many

```java
@Entity
public class Address {
  @Id
  @GeneratedValue()
  private Long id;

  private String street;

  @ManyToMany(mappedBy = "addresses")
  private List<Person> persons;
}
```

```java
@Entity
public class Person {
  @Id
  @GeneratedValue()
  private Long id;

  private String name;

  @ManyToMany
  private List<Address> addresses;
}
```

### Fetching

```java
@Entity
public class Address {
  @Id
  @GeneratedValue()
  private Long id;

  private String street;

  @OneToMany(mappedBy = "address", cascade = CascadeType.ALL, fetch = FetchType.LAZY) // default oder EAGER
  @JoinColumn(name = "address_id")
  private List<Person> persons;
}
```

## Repository

```java
@ApplicationScoped
public class AddressRepository {

  @Inject
  EntityManager em;

  public Address save(Address address) {
    if (address.getId() == null) {
      em.persist(address);
      return address;
    } else {
      return em.merge(address);
    }
  }

  public Address findById(Long id) {
    return em.find(Address.class, id);
  }

  public void deleteById(Long id) {
    Address address = findById(id);
    em.remove(address);
  }
}
```

## Panache

### Entity

```java
@Entity
public class Address extends PanacheEntity {
  private String street;
}
```

### Entity without automatic ID

```java
@Entity
public class Address extends PanacheEntityBase {
  @Id
  @GeneratedValue()
  private Long id;

  private String street;
}
```

### Named Query

```java
@Entity
@NamedQueries({
  @NamedQuery(name = "Address.findAll", query = "from Address"),
  @NamedQuery(name = "Address.findForStreet", query = "from Address where street = ?1")
})
public class Address extends PanacheEntity {
  private String street;
}

return Address.find("#Address.findForStreet", street);

```

### Repository

```java
@ApplicationScoped
public class AddressRepository implements PanacheRepository<Address> {

  public Address save(Address address) {
    if (address.getId() == null) {
      persist(address);
      return address;
    } else {
      return getEntityManager().merge(address);
    }
  }

  public Address findById(Long id) {
    return find("id", id).firstResult();
  }

  public void deleteById(Long id) {
    Address address = findById(id);
    delete(address);
  }
}
```

## Resource

```java
@Path("/api")
public class AdressResource {
  @Inject
  AddressRepository addressRepository;

  @GET
  @Path("/addresses")
  @Produces(MediaType.APPLICATION_JSON)
  public List<Address> getAddresses() {
    return addressRepository.listAll();
  }

  @GET
  @Path("/addresses/{id}")
  @Produces(MediaType.APPLICATION_JSON)
  public Address getAddress(@PathParam("id") Long id) {
    return addressRepository.findById(id);
  }

  @POST
  @Path("/addresses")
  @Consumes(MediaType.APPLICATION_JSON)
  @Produces(MediaType.APPLICATION_JSON)
  public Address createAddress(Address address) {
    return addressRepository.save(address);
  }

  @PUT
  @Path("/addresses")
  @Consumes(MediaType.APPLICATION_JSON)
  @Produces(MediaType.APPLICATION_JSON)
  public Address updateAddress( Address address) {
    return addressRepository.save(address);
  }

  @DELETE
  @Path("/addresses/{id}")
  @Produces(MediaType.APPLICATION_JSON)
  public void deleteAddress(@PathParam("id") Long id) {
    addressRepository.deleteById(id);
  }

  //with query param
  @GET
  @Path("/addresses")
  @Produces(MediaType.APPLICATION_JSON)
  public List<Address> getAddresses(@QueryParam("street") String street) {
    return addressRepository.list("street", street);
  }
}
```

## Websocket

### Server

```java
@ServerEndpoint("/websocket")
public class WebSocketServer {

  Set<Session> sessions = new HashSet<>();

  @OnOpen
  public void onOpen(Session session) {
    System.out.println("Connected ... " + session.getId());
  }

  @OnMessage
  public String onMessage(String message, Session session) {
    System.out.println("Message from " + session.getId() + ": " + message);
    return "Server: " + message;
  }

  @OnClose
  public void onClose(Session session, CloseReason closeReason) {
    System.out.println(String.format("Session %s close because of %s", session.getId(), closeReason));
  }

  @OnError
  public void onError(Session session, Throwable throwable) {
    System.out.println("Error: " + throwable.getMessage());
  }

  public void broadcast(Message message) {
    String msgString= "{}";
    try {
      msgString = objectMapper.writeValueAsString(msg);
    } catch (JsonProcessingException e) {
      log.severe(e.getMessage());
    }
    String sendMsg = msgString;
    sessions.values().forEach(s -> {
      s.getAsyncRemote().sendObject(sendMsg, result -> {
        if (result.getException() != null) {
          log.severe("Unable to send message: " + result.getException());
        }
      });
    });
  }
}
```
