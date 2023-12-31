# Introduction to services and dependency injection

*Service* is a broad category encompassing any value, function, or feature that an application needs.
A service is typically a class with a narrow, well-defined purpose.
It should do something specific and do it well.

Angular distinguishes components from services to increase modularity and reusability.

Ideally, a component's job is to enable only the user experience.
A component should present properties and methods for data binding to mediate between the view and the application logic. The view is what the template renders and the application logic is what includes the notion of a *model*.

A component should use services for tasks that don't involve the view or application logic. Services are good for tasks such as fetching data from the server, validating user input, or logging directly to the console. By defining such processing tasks in an *injectable service class*, you make those tasks available to any component.
You can also make your application more adaptable by injecting different providers of the same kind of service, as appropriate in different circumstances.

Angular doesn't *enforce* these principles.
Instead, Angular helps you *follow* these principles by making it easy to factor your application logic into services. In Angular, *dependency injection* makes those services available to components.

## Service examples

Here's an example of a service class that logs to the browser console.

<code-example header="src/app/logger.service.ts (class)" path="architecture/src/app/logger.service.ts" region="class"></code-example>

Services can depend on other services.
For example, here's a `HeroService` that depends on the `Logger` service, and also uses `BackendService` to get heroes.
That service in turn might depend on the `HttpClient` service to fetch heroes asynchronously from a server.

<code-example header="src/app/hero.service.ts (class)" path="architecture/src/app/hero.service.ts" region="class"></code-example>

## Dependency injection (DI)

<div class="lightbox">

<img alt="Service" class="left" src="generated/images/guide/architecture/dependency-injection.png">

</div>

Dependency injection (DI) is the part of the Angular framework that provides components with access to services and other resources.
Angular provides the ability for you to *inject* a service into a component to give that component access to the service.

Add the `@Injectable()` decorator to a service class so that Angular can inject it into a component as a *dependency*; the optional argument tells Angular where to register this class by default.

   <code-example path="architecture/src/app/hero.service.ts" region="provide">
   </code-example>

* Something *injectable* must be registered with an *injector* before it can be created and used. 

* Register an injectable with a *provider*, an object that tells an injector how to obtain or create a dependency. For a service class, the provider is typically the class itself.

* You don't have to create injectors. Under the hood Angular creates an application-wide *root injector* for you during the bootstrap process. It creates additional child injectors as needed.


<div class="alert is-helpful">

An injectable dependency doesn't have to be a class &mdash; it could be a function, for example, or a value.

</div>

When Angular creates a new instance of a component class, it determines which services or other dependencies that component needs by looking at the constructor parameter types.
For example, the constructor of `HeroListComponent` needs `HeroService`.

<code-example header="src/app/hero-list.component.ts (constructor)" path="architecture/src/app/hero-list.component.ts" region="ctor"></code-example>

When Angular discovers that a component depends on a service, it first checks if the injector has any existing instances of that service.
If a requested service instance doesn't yet exist, the injector makes one using the registered provider and adds it to the injector before returning the service to Angular.

When all requested services have been resolved and returned, Angular can call the component's constructor with those services as arguments.

The process of `HeroService` injection looks something like this.

<div class="lightbox">

<img alt="Service" class="left" src="generated/images/guide/architecture/injector-injects.png">

</div>

### Providing services

You must register at least one *provider* of any service you are going to use.
The provider can be part of the service's own metadata, making that service available everywhere, or you can register providers with specific components.
You register providers in the metadata of the service \(in the `@Injectable()` decorator\) or `@Component()` metadata

*   By default, the Angular CLI command [`ng generate service`](cli/generate) registers a provider with the root injector for your service by including provider metadata in the `@Injectable()` decorator.
    The tutorial uses this method to register the provider of `HeroService` class definition.

   <code-example header="hero.service.ts (provide in root)" path="architecture/src/app/hero.service.ts" region="provide">
   </code-example>

   When you provide the service at the root level, Angular creates a single, shared instance of `HeroService`
   and injects it into any class that asks for it.
   Registering the provider in the `@Injectable()` metadata also allows Angular to optimize an app
   by removing the service from the compiled application if it isn't used, a process known as *tree-shaking*.

*   When you register a provider at the component level, you get a new instance of the service with each new instance of that component.
    At the component level, register a service provider in the `providers` property of the `@Component()` metadata.

   <code-example header="src/app/hero-list.component.ts (component providers)" path="architecture/src/app/hero-list.component.ts" region="providers"></code-example>

For more detailed information, see the [Dependency Injection](guide/dependency-injection) section.

<!-- links -->

<!-- external links -->

<!-- end links -->

@reviewed 2023-09-25
