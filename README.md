

# Profile Indexer API

PreRequisites to execute:
- Postgres 9.6
- Ruby 2.6.3
- Docker >17  ~~_Optional_

## Running the API

### Local Setup
You can either use Rbenv/RVM or Docker-Compose to run the project. If you prefer local setups, you can just:
```bash
bundle install
```
After installing _**Ruby version 2.6.3**_. Then you should create a Postgres database, so Rails can communicate with:
```bash
bundle exec rails db:create
```
also, after that you can load the generated SQL tables with:
```bash
bundle exec rails db:schema:load
```
Now you're all set up. Just run
```bash
bundle exec rails server -p 3000 -b localhost
```
to get the API serving.

### Containerized Setup

If you prefer to use Docker-Compose you can just:
```bash
docker-compose up
```
then it's all done!

No matter what you kind of setup you've chosen(Local or Containerized), after complete the steps go to `http://localhost:3000` on your preferred browser and you'll see the Rails welcome page. 

# Profile Indexer (FrontEnd)

PreRequisites to execute:
- Node 8 or higher (Node 11 recommended)
- Yarn PKG manager
- Docker >17  ~~_Optional_

## Running the FrontEnd

### Local Setup
You can either use Node with Yarn or Docker-Compose to run the project. If you prefer local setups, you can just:
```bash
yarn install
```
After installing your preferred Node version. Then you can serve the Quasar App using;
```bash
yarn dev
```
### Containerized Setup

If you prefer to use Docker-Compose you can just:

```bash
docker-compose up
```
it automatically connects to our Rails API through the defined docker network.

No matter what you kind of setup you've chosen(Local or Containerized), after completing the steps go to `http://localhost:8080` on your preferred browser and you'll see our Profile Indexer up and running! 


# Useful Notes

## Microsservice-like architecture

As noticed, I have opted to use a microsservice like architecture. Meaning that we have Backend, Database and FrontEnd all split up. What do we gain from it?
Choosing Microsservices over Monolith provides an extensive set of advantages, among those we get:
- Better Organization: Each microsservice does a single job, and also makes it easier to attach an additional service. Thus we have much flexibility.
- Decoupling: By using microsservices architecture makes it easier to refactor our services.

Those "principles" are present all along the code, especially in the API. You'll see I have two concerns in my `àpp/models/concerns/` folder. One wraps the URLShortening requisite, and the other manages Twitter Spyding (_aka_ WebCrawling, Scrapping, Crawling). Why using concerns and not placing the entire logic in `Profile`  model? To get flexibility aournd the code! By making this decision, I've made more straightforward, for example, to add crawling methods or change the way the URLShortening is made, by changing the gem, for instance. We only would have to change the concern, and not anywhere else in code, something like an `Adapter` design pattern does.

The logic of Searching through the `Profile` instances has been placed in its own separated controller too, what keeps accordance with Rails' RESTful architecture.

When it comes to the FrontEnd, using Quasar Framework has made it possible to get a component architecture, this means that the FrontEnd is built with independent, modular, and reusable parts. This architecture has also made it possible to develop faster, and provided some of the same advantages I described in microsservices. 

Not only that but this components architecture makes pretty easy to make the frontend reactive, and it is totally under Rails' DRY principle since it permits the usage of `mixins`, some helper-like definitions we can write to reuse our code. Some of those can be found at `src/mixins/`folder. Choosing a framework such as Quasar has also made it easy to create components since it sets a standard, either visual or inside the code.

## DevOps

### Docker
I have used Docker to get a faster setup of my development environment; for this, I have written a few scripts to make lifting our environment pretty easy.

### Continuous Integration

When it comes to Continuous Integration, I have used TravisCI for both repositories (BackEnd and FrontEnd) because it has better integration with Github. I have used `git` to control both of my projects development and get near real-time feedback with TravisCI. I have defined multistaged builds for both projects with parallel jobs, and to get even faster builds, I have cached the bundle and yarn dependencies installation.

### Heroku
In order to provide the Indexer on the Web I have published both API and APP. For the API I have used Heroku and provisioned a Postgres Database for it.

### Surge
For the APP, I have used Surge and connected it with the API via _Axios_. The published app can be accessed in [http://indexer.surge.sh/#/](http://indexer.surge.sh/#/).

## Improvements achieved

The most notable improvements achieved with those projects were 100% test coverage in the API using _Minitest_ and _Mocha_, to confirm this coverage, I have set _SimpleCov_ that provides this test coverage percentage. You can run `bundle exec rails test` and it'll generate a folder in the API project root dir named `coverage/`, inside there you can open the `index.html`  file in your preferred browser to get an overview of test coverage.

Also, to get a clean and standardized coding, I have set EsLint and Rubocop linters to take care of setting patterns along the Backend and Frontend codebases. Both integrated with the Travis continuous integration pipeline. Rubocop's stylesheet rules are defined in `.rubocop.yml` file and ESLint's Air BnB stylesheet is defined in `.eslintrc.js`
