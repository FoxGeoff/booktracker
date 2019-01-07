# Booktracker

This project was generated with [Angular CLI](https://github.com/angular/angular-cli) version 1.2.0.

## Development server

Run `ng serve` for a dev server. Navigate to `http://localhost:4200/`. The app will automatically reload if you change any of the source files.

## Code scaffolding

Run `ng generate component component-name` to generate a new component. You can also use `ng generate directive|pipe|service|class|module`.

## Build

Run `ng build` to build the project. The build artifacts will be stored in the `dist/` directory. Use the `-prod` flag for a production build.

## Running unit tests

Run `ng test` to execute the unit tests via [Karma](https://karma-runner.github.io).

## Running end-to-end tests

Run `ng e2e` to execute the end-to-end tests via [Protractor](http://www.protractortest.org/).
Before running the tests make sure you are serving the app via `ng serve`.

## Further help

To get more help on the Angular CLI use `ng help` or go check out the [Angular CLI README](https://github.com/angular/angular-cli/blob/master/README.md).

## Consumming a Rest Service (See Power Point)
* CRUD => REST and HTTP codes
* Subscribing to Observables

## Check: Get all books from a RESTful service
* calling function:
```
ngOnInit() {
    this.dataService.getAllBooks().subscribe(
      (data: Book[]) => this.allBooks = data,
      (err: any) => console.log(err),
      () => console.log('all done getting books.')
    );
```
* Data service:
```
getAllBooks(): Observable<Book[]> {
    console.log('Getting all books from the server');
    return this.http.get<Book[]>('/api/books');
  }
```
## Check: Get one book from a RESTful service
* calling function:
```
ngOnInit() {
    let bookID: number = parseInt(this.route.snapshot.params['id']);
    this.dataService.getBookById(bookID)
      .subscribe(
        (data: Book) => this.selectedBook = data,
        (err: any) => console.log(err),
        () => console.log('complete getting book ${bookID}')
      );
```
* Data Service:
```
getBookById(id: number): Observable<Book> {
    console.log('Getting id: ${id} book from the server');
    return this.http.get<Book>('/api/books/${id}');
  }
```
## Check: Get headers
*
```
getBookById(id: number): Observable<Book> {
    let getHeaders: HttpHeaders = new HttpHeaders({
      'Accept': 'application/json',
      'Authorization': 'my-token'
    });

    console.log('Getting book from the server id: ' + id );
    return this.http.get<Book>(`/api/books/${id}`, {
      headers: getHeaders
    });
  }
```
* Refactor:
```
getBookById(id: number): Observable<Book> {
    console.log('Getting book from the server id: ' + id );
    return this.http.get<Book>(`/api/books/${id}`, {
      headers: new HttpHeaders({
        'Accept': 'application/json',
        'Authorization': 'my-token'
      })
    });
  }
```
## Check: Transforming Data with RxJS
* calling
```
this.dataService.getOldBookById(bookID)
      .subscribe(
        (data: OldBook) => console.log(`Old book title: ${data.bookTitle}`)
      );
```
* Service
```
getOldBookById(id: number): Observable<OldBook> {
    return this.http.get<Book>(`/api/books/${id}`)
      .pipe(
        map(b => <OldBook>{
          bookTitle: b.title,
          year: b.publicationYear
        }),
        tap(clasicBook => console.log(clasicBook))
      );
  }
```
## Create, Update and Delete
* addBook:
```
addBook(newBook: Book):Observable<Book>{
    return this.http.post<Book>('/api/books',newBook, {
      headers: new HttpHeaders({
        'Content': 'application/json'
      })
    })
  }
```
* updateBook:
```
updateBook(updatedBook: Book):Observable<void>{
    return this.http.put<void>(`/api/books/${updatedBook.bookID}`,updatedBook, {
      headers: new HttpHeaders({
        'Content': 'application/json'
      })
    });
  }
```
* deleteBook:
```
 deleteBook(bookID: number): Observable<void> {
    return this.http.delete<void>(`api/books/${bookID}`);
  }
```
## Check CRUD component calls
* add-book.component
```
saveBook(formValues: any): void {
    let newBook: Book = <Book>formValues;
    newBook.bookID = 0;
    console.log(newBook);

    this.dataService.addBook(newBook)
      .subscribe(
        (data:Book) => console.log(data),
        (err: any) => console.log(err)
      );
  }
```