# sew-matura-hilfszettel

# Angular

## Bindings

```html
<p>{{ text }}</p>
<button [disabled]="text == 'Hello World!'" (click)="handleClick()">btn</button>
<input [(ngModel)]="name">
```

## Direktiven

```html
<p *ngIf="name !== ''">Hello {{ name }}</p>
<p [ngStyle]="color: getColor()" [ngClass]="type: getTextType()"></p>
<ul>
	<li *ngFor="let user of users; let i = index">{{ i }} - {{ user }}</li>
</ul>
```

## Components Databinding

```tsx
@Input('name') name!: string;
@Output() removeEvent = new EventEmitter();

remove() {
this.removeEvent.emit();
}
```

```html
<p #pTag>gets removed</p>
<some-component [name]="name" (removeEvent)="handleRemoveEvent(pTag)"></some-component>
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
<router-outlet></router-outlet>
<a routerLink="/gallery">Gallery</a>
```

```tsx
constructor(private router: Router, private route: ActivatedRoute) {}

this.router.navigate(['gallery']);
this.router.navigate(['gallery'], {relativeTo: route});
this.router.navigate(['gallery', index]);
```

```tsx
constructor(private route: ActivatedRoute) {}

ngOnInit() {
	this.pathParam = route.snapshot.params['param'];
}
```
