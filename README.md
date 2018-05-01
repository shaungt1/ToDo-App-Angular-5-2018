# ToDo-App-Angular-5-2018
This is a frontside Evolution Course project  for a Todo post API application. this is part 1 of 6 where we will convert the post type todo application into a full social media style clone.


# ToDo App Part 1 Of 6

## Add  Todo Class: 
- First add a new class and unit testing file  by adding a new folder to  “src” called Todo.class
- Type in terminal to generate the new class.$ ng generate class Todo --spec
  *(you might have to move the generated files into the folder after you create the files to the new directory you just made!)

#### Add The following code to the new class file  *src/app/Todo.class/Todo.ts 

    export class Todo {
      id: number;
      title: string = '';
      complete: boolean = false;

      constructor(values: Object = {}) {
        Object.assign(this, values);
      }
    }

*Add logic to run your test unit. It is important to understand how to run test units to ensure your code and constructor will operate seamlessly. 

## Add the Following code to “Todo.spec.ts” File:

      import {Todo} from './todo';*


      describe('Todo', () => {
        it('should create an instance', () => {
          expect(new Todo()).toBeTruthy();
        });
      });


- *Run the unit test typing ng test in the terminal. This will launch the jasmine server and Karma testing library to check the validity of our new class.

- *You should see a few errors! The errors for “title “h1” from the string interpolation that was set up when we generated the Angular  project. This is perfectly fine we will modify the test later. You should see know errors for the Todo.spec.ts unit test.*

# Add Todo Services
*Add a new folder to the “src” directory call “Services” and then type the command below in the terminal to generate a new services and unit testing file. 
(you might have to move the generated files into the folder after you create the files to the new directory you just made!)

##### Add this command: 
### $ ng g service TodoData

# Add This code to the “Service.Ts” File


      import { Injectable } from '@angular/core';
      import {Todo} from '../Todo.class/todo';
      @Injectable()
      export class TodoDataService {

       // Placeholder for last id so we can simulate
       // automatic incrementing of ids
       lastId  = 0;

       // Placeholder for todos
       todos: Todo[] = [];

       constructor() {
       }

       // Simulate POST /todos
       addTodo(todo: Todo): TodoDataService {
         if (!todo.id) {
           todo.id = ++this.lastId;
         }
         this.todos.push(todo);
         return this;
       }

       // Simulate DELETE /todos/:id
       deleteTodoById(id: number): TodoDataService {
         this.todos = this.todos
           .filter(todo => todo.id !== id);
         return this;
       }

       // Simulate PUT /todos/:id
       updateTodoById(id: number, values: Object = {}): Todo {
         let todo = this.getTodoById(id);
         if (!todo) {
           return null;
         }
         Object.assign(todo, values);
         return todo;
       }

       // Simulate GET /todos
       getAllTodos(): Todo[] {
         return this.todos;
       }

       // Simulate GET /todos/:id
       getTodoById(id: number): Todo {
         return this.todos
           .filter(todo => todo.id === id)
           .pop();
       }

       // Toggle todo complete
       toggleTodoComplete(todo: Todo){
         let updatedTodo = this.updateTodoById(todo.id, {
           complete: !todo.complete
         });
         return updatedTodo;
       }
      }
      }

 ## Add A new Component
##### To generate a  new angular component Type this code in the terminal: 
 ###### $ ng g c Todo
This will generate a new component so we can build are main view. Ad the code below to “template” “app/Todo/Todo.component.html” 

## Add Bootstrap 4 to Angular 5

*Use your terminal inside the Angular application to  add the bootstrap 4 library and dependencies by typing this command in the bash terminal.

#####* MAKE SURE YOU HAVE THE LATEST VERSION OF NODE.JS INSTALLED!!!

#### $ npm install bootstrap --save

- Next add this import to the project to propagate the Bootstrap library to take effect over our Angular project. 
- To make the Bootstrap CSS classes available for the components in our project we need to include the Bootstrap CSS file from node_modules/bootstrap/dist/css/bootstrap.css in our project. To do so add the following line in file styles.css:

# Add this to global Style.css file located in “app/styles.css”

    @import "~bootstrap/dist/css/bootstrap.css"
    
# TodoData Service
#### Add this code to the unit test “Services.spec.ts” File

    import {TestBed, async, inject} from '@angular/core/testing';
    import {Todo} from '../Todo.Class/todo';
    import { TodoDataService } from './todo-data.service';


    describe('TodoDataService', () => {
     beforeEach(() => {
       TestBed.configureTestingModule({
         providers: [TodoDataService]
       });
     });

     it('should ...', inject([TodoDataService], (service: TodoDataService) => {
       expect(service).toBeTruthy();
     }));

     describe('#getAllTodos()', () => {

       it('should return an empty array by default', inject([TodoDataService], (service: TodoDataService) => {
         expect(service.getAllTodos()).toEqual([]);
       }));

       it('should return all todos', inject([TodoDataService], (service: TodoDataService) => {
         let todo1 = new Todo({title: 'Hello 1', complete: false});
         let todo2 = new Todo({title: 'Hello 2', complete: true});
         service.addTodo(todo1);
         service.addTodo(todo2);
         expect(service.getAllTodos()).toEqual([todo1, todo2]);
       }));

     });

     describe('#save(todo)', () => {

       it('should automatically assign an incrementing id', inject([TodoDataService], (service: TodoDataService) => {
         let todo1 = new Todo({title: 'Hello 1', complete: false});
         let todo2 = new Todo({title: 'Hello 2', complete: true});
         service.addTodo(todo1);
         service.addTodo(todo2);
         expect(service.getTodoById(1)).toEqual(todo1);
         expect(service.getTodoById(2)).toEqual(todo2);
       }));

     });

     describe('#deleteTodoById(id)', () => {

       it('should remove todo with the corresponding id', inject([TodoDataService], (service: TodoDataService) => {
         let todo1 = new Todo({title: 'Hello 1', complete: false});
         let todo2 = new Todo({title: 'Hello 2', complete: true});
         service.addTodo(todo1);
         service.addTodo(todo2);
         expect(service.getAllTodos()).toEqual([todo1, todo2]);
         service.deleteTodoById(1);
         expect(service.getAllTodos()).toEqual([todo2]);
         service.deleteTodoById(2);
         expect(service.getAllTodos()).toEqual([]);
       }));

       it('should not removing anything if todo with corresponding id is not found', inject([TodoDataService], (service: TodoDataService) => {
         let todo1 = new Todo({title: 'Hello 1', complete: false});
         let todo2 = new Todo({title: 'Hello 2', complete: true});
         service.addTodo(todo1);
         service.addTodo(todo2);
         expect(service.getAllTodos()).toEqual([todo1, todo2]);
         service.deleteTodoById(3);
         expect(service.getAllTodos()).toEqual([todo1, todo2]);
       }));

     });

     describe('#updateTodoById(id, values)', () => {

       it('should return todo with the corresponding id and updated data', inject([TodoDataService], (service: TodoDataService) => {
         let todo = new Todo({title: 'Hello 1', complete: false});
         service.addTodo(todo);
         let updatedTodo = service.updateTodoById(1, {
           title: 'new title'
         });
         expect(updatedTodo.title).toEqual('new title');
       }));

       it('should return null if todo is not found', inject([TodoDataService], (service: TodoDataService) => {
         let todo = new Todo({title: 'Hello 1', complete: false});
         service.addTodo(todo);
         let updatedTodo = service.updateTodoById(2, {
           title: 'new title'
         });
         expect(updatedTodo).toEqual(null);
       }));

     });

     describe('#toggleTodoComplete(todo)', () => {

       it('should return the updated todo with inverse complete status', inject([TodoDataService], (service: TodoDataService) => {
         let todo = new Todo({title: 'Hello 1', complete: false});
         service.addTodo(todo);
         let updatedTodo = service.toggleTodoComplete(todo);
         expect(updatedTodo.complete).toEqual(true);
         service.toggleTodoComplete(todo);
         expect(updatedTodo.complete).toEqual(false);
       }));

     });

    });`


# Modify The “Todo” Component
##### Add This code to the “Todo Component.css” template

    /* Custom Fullstack SCSS*/
    .checkmark {
     height: 25px;
     width: 25px;
     background-color: #eee;
     border-radius: 50%;
    }

    .pad{
     padding-inline-start: 10px;
    }


    Add This code to the “Todo Component.html” template
    <section class="todoapp">
     <header class="header">
       <br/>
       <div class="pad card border-success mb-3" >
       <h6 class="card-text text-success" >Enter Things You Need To-Do Below And Press Enter!</h6>
     </div>
       <input class="new-todo form-control card-body text-dark " placeholder="What needs to be done?"
       autofocus="" [(ngModel)]="newTodo.title" (keyup.enter)="addTodo()">
     </header>
     <br/>
     <section class="main card bg-dark " *ngIf="todos.length > 0">
       <ul class="todo-list list-group list-group-flush  ">
         <li class= " list-group-item card bg-light mb-4  " *ngFor="let todo of todos" [class.completed]="todo.complete">
           <div class="view card-body ">
             <div align="left" class="">
             <input class="toggle checkmark"  type="checkbox" (click)="toggleTodoComplete(todo)" [checked]="todo.complete">
             <p>Task Complete</p>
             <label class="list-group-item" >{{todo.title}}</label>
           </div>
           <div class="card-title " >
                <button class="destroy btn btn-secondary" (click)="removeTodo(todo)">Delete Post</button>
           </div>
         </div>
         </li>
       </ul>
     </section>
    <br/>
     <footer class="footer navbar navbar-dark bg-dark" *ngIf="todos.length > 0">
       <span class="todo-count navbar-brand "><strong>{{todos.length}}</strong> {{todos.length == 1 ? 'item' : 'items'}} left</span>
     </footer>
    </section>


# Add this Code to the Todo.Component.ts Typescript file

      import { Component } from '@angular/core';
      import {Todo} from '../../Todo.class/todo';
      import {TodoDataService} from '../../Services/todo-data.service';
      @Component({
        selector: 'app-todo',
        templateUrl: './todo.component.html',
        styleUrls: ['./todo.component.css'],
        providers: [TodoDataService]
      })
      export class TodoComponent  {
        newTodo: Todo = new Todo();
        constructor(private todoDataService: TodoDataService) {
        }

        addTodo() {
          this.todoDataService.addTodo(this.newTodo);
          this.newTodo = new Todo();
        }
        toggleTodoComplete(todo) {
          this.todoDataService.toggleTodoComplete(todo);
        }
        removeTodo(todo) {
          this.todoDataService.deleteTodoById(todo.id);
        }
        get todos() {
          return this.todoDataService.getAllTodos();
        }
      }



# Configure Root App Component.

#### Configure The App Root Template
- The root App Component needs to be edited a bit. First we will go to the “app.component.html”  THE TEMPLATE
Delete Everything in the template

#### Add this code below to the template

##### <Todo-App> </Todo-App>

###### This will add the entire Todo component and inject it to the DOM through the App-Root component. 

# Add a Bootstrap NAV Header to the APP TEMPLATE
- Go to https://getbootstrap.com/docs/4.1/examples/  and click on any of the sample templates. Right click on them and  >> View Page  Source >> Copy the block of HTML code for the navigation bar. 

# Add this to the App Root component Template.

### MAKE SURE TO ADD THE “TODO-COMPONENT” DIRECTIVE UNDER THE HEADER. 

- Customize it however you want and add an image for the header and put it in you “assets” folder. 
If you want to use the angular favicon.ico you need to use this file path:  “../favicon.ico”
Or Copy This Code 

      <!-- Bootstrap Nav Bar -->
      <nav class="navbar navbar-dark bg-dark">
         <a class="navbar-brand" href="#">
           <img src="../favicon.ico" width="30" height="30" class="d-inline-block align-top" alt="">
           Angular 5 Todo App
         </a>
       </nav>


# Configure Root App Typescript File.
*Make sure everything is cleared out of the component “class” between the { } we don't want any functions left in there.

# Configure Root App Component Unit test.

##### Go ahead and run a test to see what happens and what propagates?
- You will notice that there are a few errors. This is because the original unit test was testing for the conditions that it can pre packaged and shipped with when we started and generated the App.
- Go ahead and delete all the code in the unit test if you like or leave it so you can reuse it and modify later. Play around with writing different unit test.


# Phase 1 of the TODO app is now complete! see video for more details and instructions!!!



# Deploying to GitHub Pages
Angular CLI makes it super simple to deploy our application to GitHub Pages with a single command like this:
$ ng github-pages:deploy --message 'deploy(dist): deploy on GitHub pages'

The github-pages:deploy command tells Angular CLI to build a static version of our Angular application and push it to the gh-pages branch of our GitHub repository:

- $ ng github-pages:deploy --message 'deploy(dist): deploy on GitHub pages'
- Built project successfully. Stored in "dist/".
- Deployed! Visit 
- Github pages might take a few minutes to show the deployed site.


#### The MAIN application is available at 



#### App should be ready to go and Look Good! We will go more in depth in the overall tutorial by implementing the features listed below.

<hr>
# Deploy Angular 5 App to Bluehost or Any Other Host!

#### Here are the instructions on how to get in deploy your angular application. 

- add this line to the terminal: ng build --prod --aot 

- This will build a production version of your angular application with an arrow. Tyler which takes your angular HTML and typescript code and converts it into efficient JavaScript. 

- You also need to specify the directory online it's going to be in through your: 
 <base href="/"> this is located in your "index.html" file in your angular app. 

- If you are setting your built app up on a brand new host and you have a domain name then you do not need to change the <base href="/"> 

- If you are using a subdirectory or have to create a folder in your hosting that has a domain assigned to it already then whatever the name of the folder is that you create needs to be set in the <base href="/My-Angular-5-App"> 

- You can set this up and save it and you're at before you run your terminal  command "ng build --prod --aot " 

- Or you can simply just run this command all together to change the <base href="/">  and build a production distribution 

      ng build --prod --aot --base-href  /Your-new-app-folder/ 

### Note: 
#### Make sure that you add the following forward slash / after the name of the folder you created. 

## Also make sure only to add the files from the "dist folder" and not the dist folder itself. This makes another step through the directory in your file path the app will not know about. 

#### You can watch the full video that I made for complete instructions and complete explanation on everything. enjoy!
By: Shaun Pritchard;  Frontside Evolution

- Visit me or leave comments and questions @ frontsideseo.com

- Check out full working App Distribution from this course here:

http://www.surfingthecoast.com/My-Angular5-App/



 


