# 12 Factor Appetizer

This is an attempt at summarizing the 12 tenents of [The Twelve-Factor App](http://12factor.net/).

Their introduction:
> In the modern era, software is commonly delivered as a service: called web apps, or software-as-a-service. The twelve-factor app is a methodology for building software-as-a-service apps that:

> * Use declarative formats for setup automation, to minimize time and cost for new developers joining the project;
> * Have a clean contract with the underlying operating system, offering maximum portability between execution environments;
> * Are suitable for deployment on modern cloud platforms, obviating the need for servers and systems administration;
> * Minimize divergence between development and production, enabling continuous deployment for maximum agility;
> * And can scale up without significant changes to tooling, architecture, or development practices.
>
> The twelve-factor methodology can be applied to apps written in any programming language, and which use any combination of backing services (database, queue, memory cache, etc).

According to them, any developer building applications which run as a service and ops engineers who deploy or manage such applications should know about The Twelve Factor App.

## 1. Codebase
 * A twelve-factor app is always tracked in a version control system. (Git, Mercurial, Subversion)
 * There is always a one-to-one correlation between the codebase and the app.
  * If there are multiple codebases, it’s not an app – it’s a distributed system.
  * Multiple apps sharing the same code is a violation of twelve-factor. Shared code should be abstracted into libraries that can be included via a dependency manager (aka [npm](https://www.npmjs.com/)).
 * While there is only one codebase, there can be many running instances of the app.
 * Codebase is the same accross all deploys but can be different versions (think dev stack, qa stack, production stack).

## 2. Dependencies
 * Explicitly declare and isolate dependencies
 * It declares all dependencies, completely and exactly, via a dependency declaration manifest. (`Gemfile` or `package.json`).
 * One benefit of explicit dependency declaration is that it simplifies setup for developers new to the app.
 
## 3. Config
 * Store config in the environment
 * Another approach to config is the use of config files which are not checked into revision control. (`database.yml` in Rails)
 * Could codebase be made open source at any moment, without compromising any credentials?
 * Env vars are never grouped together as “environments”, but instead are independently managed for each deploy.
 
## 4. Backing Services
 * Treat backing services as attached resources
 * A backing service is any service the app consumes over the network as part of its normal operation.
  * examples include: datastores (Postgres), messaging/queueing systems (RabbitMQ), and caching systems (Memcached or Redis)
 * The code for a twelve-factor app makes no distinction between local and third party services.

> A deploy of the twelve-factor app should be able to swap out a local MySQL database with one managed by a third party (such as Amazon RDS) without any changes to the app’s code. 

## 5. Build, release, run
 * Strictly separate build and run stages
 * A codebase is transformed into a (non-development) deploy through three stages:
  * The build stage is a transform which converts a code repo into an executable bundle known as a build. Using a version of the code at a commit specified by the deployment process, the build stage fetches and vendors dependencies and compiles binaries and assets.
  * The release stage takes the build produced by the build stage and combines it with the deploy’s current config. The resulting release contains both the build and the config and is ready for immediate execution in the execution environment.
  * The run stage (also known as “runtime”) runs the app in the execution environment, by launching some set of the app’s processes against a selected release.
 
## 6. Processes
 * Execute the app as one or more stateless processes (According to Powell, this is no longer us)
 * Twelve-factor processes are stateless and share-nothing. What data I store locally should not exist in production.
 * The twelve-factor app never assumes that anything cached in memory or on disk will be available on a future request or job.

## 7. Port binding
 * Export services via port binding
 * The web app exports HTTP as a service by binding to a port, and listening to requests coming in on that port. (?)
  
## 8. Concurrency
 * Scale out via the process model 
 * In the twelve-factor app, processes are a first class citizen.
 * HTTP requests may be handled by a web process, and long-running background tasks handled by a worker process

## 9. Disposability
 * Maximize robustness with fast startup and graceful shutdown
 * The twelve-factor app’s processes are disposable, meaning they can be started or stopped at a moment’s notice.

## 10. Dev/prod parity
 * Keep development, staging, and production as similar as possible
 * Historically there have been large gaps between development and production:
  * The time gap: A developer may work on code that takes days, weeks, or even months to go into production.
  * The personnel gap: Developers write code, ops engineers deploy it.
  * The tools gap: Developers may be using a stack like Nginx, SQLite, and OS X, while the production deploy uses Apache, MySQL, and Linux.
 * The twelve-factor app is designed for continuous deployment by keeping the gap between development and production small.
  * a developer may write code and have it deployed hours or even just minutes later.
  * developers who wrote code are closely involved in deploying it and watching its behavior in production.
  * keep development and production as similar as possible.

## 11. Logs
 * Treat logs as event streams
 * A twelve-factor app never concerns itself with routing or storage of its output stream.

## 12. Admin processes
 * Run admin/management tasks as one-off processes
 * Running database migrations (`rake db:migrate` in Rails)
 * Running a console to run arbitrary code or inspect the app’s models against the live database.
 * Running one-time scripts committed into the app’s repo
 
