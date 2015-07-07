#Active Record Intro:  Code Base

## Summary

This challenge introduces the code base with which we'll be working as we become acquainted with Active Record, the object-relational mapper that we'll be using.  Before we jump into the challenges proper, we should take a look at this code base, familiarizing ourselves with the directory structure and what each part is doing.  This directory structure mimics the structures we'll see in our Phase 2 and 3 applications.

### Environment Configuration

Going forward, the setup of our applications will be a little more complex than it has been in previous challenges.  We're going to be more intentional about how we organize our files.  Also, we'll need to do some configuration (e.g., connecting to our database).  This code base—or skeleton—provides the configuration that we'll need to interact with a database using Active Record; however, we should understand what it's doing.  

The configuration of our application is done in the file `config/environment.rb`.  It requires all the gems and Ruby libraries that we'll be using.  It loads all of our model files and sets up the database connection.  Take some time to look through the code.  We'll probably find something new, like the [`Pathname`][Ruby Docs Pathname] and [`Dir`][Ruby Docs Dir] classes and the configuration of `ActiveRecord::Base` to connect with the database.  It's okay if not all of this makes perfect sense right now, but we should have the gist of what's happening ... if not exactly how.

### Rakefile

```
$ bundle exec rake -T
```
*Figure 1*.  Listing available Rake tasks.

The code base also supplies a Rakefile, providing us with a number of helpful tasks to get our application up and running.  Defining Rake tasks save us time by automating tasks that we regularly do.  The tasks defined in this Rakefile mimic the tasks that we'll be working with moving forward at Dev Bootcamp (see Figure 1 for how to list available tasks).  

- `rake console`

  The console task opens IRB with our application's environment loaded:  the models are available, the connection with the database is established, etc.  Whereas before we would interact with our database from the command line by running `SQLite3`, now we'll use the rake console.  


- `rake spec`

  The spec task will run all of the spec files in our application (i.e., run the tests).  It's an alternative to the `rspec` command.

- `rake db:taskname`

  The Rakefile includes a `:db` namespace that encapsulates a number of tasks related to the database:  `create`, `drop`, `migrate`, `rollback`, `seed`, and `version`.  We'll become very familiar with these tasks over the course of Dev Bootcamp.

- `rake generate:taskname`

  Just like the `:db` namespace, the `:generate` namespace encapsulates tasks for creating or generating files:  `model` for creating files for Active Record models, `migration` for creating files for updating our database, and `spec` for creating test files for our models.

### Model-view-controller Organization

This code base is setup for implementing the model-view-controller design pattern.  The files for each will be located in individual folders under `app/`.  As we begin working with Active Record, we won't work much with controllers and views, rather we'll focus on working with Active Record models.

All of the model classes that we write will be located within the `app/models` folder.  Each class should be defined in its own file.  As an example, a `Dog` class has been written in the file `app/models/dog.rb`.  The classes in this folder do not need to be backed by the database—in other words, they don't need to inherit from `ActiveRecord::Base`, but can be normal Ruby classes.

### Database

The `db/` directory is where files associated with our database are located.  Our SQLite3 database will be located in this directory after we've created it.

We'll be creating files called *migrations* that help to build our database schema (e.g., creating tables).  All of the migrations that we write will be located within the `db/migrate` folder:  one migration for each change to the database schema.  The `db:migrate` Rake task tells `ActiveRecord::Migrator` to look here for migration files.  An example migration file, `20140901164300_create_dogs.rb`, has been provided—it will create a dogs table in our database.

Sometime we'll want to populate our database with data, so that we can use it or manually test it.  To do so, we can open the rake console and add records, or we could write a script to do it for us.  We can write Ruby code in the `seeds.rb` file.  The code is then executed through the Rake task:  `rake db:seed`.  An example seeds file has been provided that adds a couple dogs to the database.

### Organizing Test Files
All of our testing files will be located within the `spec/` directory.  All of the specs can be run with the `spec` Rake task.  An example spec file has been provided:  `spec/schema/dogs_table_spec.rb`.


### Specifying Gems
The `Gemfile` and `Gemfile.lock` files specify the gems used in this application.  Bundler will use these files to install required gems if they're not installed on our system and to determine which version of a gem to use.


## Releases

### Release 0:  Practice Working With the Code Base

Designed to get you exposure to working with Bundler, Rake tasks, and the console, this challenge is more like a tutorial.  Follow the steps below.

##### 1. Install Gems

-  From the command line, run `bundle install`.

  This command will install any necessary gems that are missing from our system.  Because the code base provides a `Gemfile.lock` file, Bundler will use this file to determine exactly which version of each gem to install.
  
  This command requires Bundler to be installed on our system.  If it's not already installed, run `gem install bundler`.

##### 2. Create the Database

- From the command line, run `bundle exec rake db:create`.

  This executes the `create` Rake task defined within the `db` namespace.  It will create the .sqlite3 database file in the `db/` directory.  Prefacing our command with `bundle exec` executes the command within the context of the gems specified for this application (i.e., we use the right version of the gems).

##### 3. Check Database Version

- From the command line, run `bundle exec rake db:version`.

  This executes the `version` Rake task defined within the `db` namespace.  In our case, we haven't run any migrations, so we should see something like `Current version: 0`.

##### 4. Run the Test Suite

- From the command line, run `bundle exec rake spec`.

  This executes the `spec` Rake task, which runs all of our tests.  In our app, we have a `dogs_table_spec.rb` file that tests the structure of our database.  There should be a `dogs` table with all the desired columns (i.e., they're properly named and of the right type).  Because we've yet to run any migrations, all the tests should fail.

##### 5. Migrate the Database

-  From the command line, run `bundle exec rake db:migrate`.

  This executes the `migrate` Rake task defined within the `db` namespace.  Any migration files that have yet to be run, will be run.
  
  Lets take a look at the output in the console.  The first line of output should read something like `CREATE TABLE "schema_migrations" ("version" ... )`.  Active Record is creating a table where it can track which migrations have been run, using the timestamp from the filename as an identifier.  The last bit of SQL run should read something like `INSERT INTO "schema_migrations" ("version") VALUES (?)  [["version", "20140901164300"]]`.  Active Record is noting that it's run our migration file, so that it won't run it again.
  
  In the middle of the output, we should see a line similar to `Migrating to CreateDogs (20140901164300)`.  Active Record is letting us know which migration is being run.  And a few lines after this one, we find the SQL statement that is executed:  `CREATE TABLE "dogs" ("id" INTEGER ... "updated_at" datetime)`.  Finally, Active Record lets us know that it's done with this migration:  `20140901164300 CreateDogs: migrated (0.0028s)`.

##### 6. Check Database Version

- From the command line, run `bundle exec rake db:version`.

  Active Record will check the `schema_migrations` table and let us know the the timestamp of the last migration that was run.  Now that we've run a migration, we should see something like `Current version: 20140901164300`.

##### 7. Run the Test Suite

- From the command line, run `bundle exec rake spec`.

  The provided `CreateDogs` migration matches the specs.  Now that we've run the migration, all of the tests should pass.

##### 8. Populate the Database with Seed Data

- From the command line, run `bundle exec rake db:seed`.

  This executes the `seed` Rake task defined within the `db` namespace.  This task requires the `db/seeds.rb` file.  As the file is read in, the code executes.  
  
  In this example, two dog records are added to the database.  Reading through the output in the console, we can see that two SQL `INSERT` statements were executed.  One for each dog.  For example, `INSERT INTO "dogs" ("age", ... ) VALUES (?, ... )  [["age", 1], ... ]`.
  
##### 9. Open the Console

- From the command line, run `bundle exec rake console`.

  This executes the `console` Rake task, opening IRB with our environment loaded.  We can interact with our models in the console.  We can select records from the database, insert new records, destroy records, etc.
  
  To sample what we can do from within the console, run ...
  
  -  `ActiveRecord::Base.connection.tables`
      
      This returns an array containing the names of the tables in our database.
  
  -  `ActiveRecord::Base.connection.columns(:dogs)`

      This returns an array of objects representing the columns in the `dogs` table—one object for each column.  The objects themselves happen to be instances of the `ActiveRecord::ConnectionAdapters::SQLite3Column` class.
  
  -  `Dog`

      This will return the `Dog` class itself, and we will see in parentheses a list of attribute names and types that are associated with instances of `Dog`.  Active Record derives these attributes from the columns in the `dogs` table in our database.
        
  - `Dog.all`

      `Dog::all` is a class method that returns all of the records in the `dogs` table as instances of the `Dog` class.  The individual instances are returned within a collection, an `ActiveRecord::Relation` object that acts much like an array.  In the console output, we can see the SQL statement that was executed: `SELECT "dogs".* FROM "dogs"`.
        
  - `exit`

     This will exit the console, just like IRB.  Alternatively, use *control + d*.

##### 10. Rollback the Database

- From the command line, run `bundle exec rake db:rollback`.

  This executes the `rollback` Rake task defined within the `db` namespace.  The default behavior is to undo the last migration, but you could specify multiple steps.  In executing this task, Active Record pulled the last timestamp from the `schema_migrations` table, found the `change` method defined there, and undid it.  In our case, the `CreateDogs` migration created the `dogs` table.  So, to rollback this migration, Active Record dropped the `dogs` table.  Any data in the table was lost.
  
  We can see in the output that Active Record executed the SQL statement `DROP TABLE "dogs"`.  It also updated the `schema_migrations` table, so that it no longer has a record of running our migration:  `DELETE FROM "schema_migrations" WHERE ... "version" = '20140901164300'`.
  
##### 11. Drop the Database

- From the command line, run `bundle exec rake db:drop`.

  This executes the `drop` Rake task defined within the `db` namespace.  In our case, it will delete our .sqlite3 file.

### Release 1:  Reflect

Once you've completed Steps 1 - 11 from Release 0, checkout a new branch and create a file in the root directory:  `reflections.txt`.  Reflect on what you've—individually and/or as a pair—learned through this challenge and write your reflects in the file.  Then commit, push your branch, and submit a pull request.

[Ruby Docs Pathname]: http://www.ruby-doc.org/stdlib-2.1.2/libdoc/pathname/rdoc/Pathname.html
[Ruby Docs Dir]: http://www.ruby-doc.org/core-2.1.2/Dir.html