#Deploying gradle java based app / postgresql to heroku
#Installations
1. Install heroku cli , create heroku account
2. Install maven software : run mvn -version : when you download the maven software for installation ensure you select the binary version for your platform.
  - On Linux: https://linuxize.com/post/how-to-install-apache-maven-on-ubuntu-18-04/ : use apt process
  - On Windows : https://devwithus.com/install-maven-windows/  : give emphasis on the set up for environmental variables 
#POM.XML 
3. Create a pom.xml file : the manifest for your app : in the root project directory
4. Look at the sample pom.xml file in this repo : read comments in the file for guide 
#Update your main.java class : usually App.java  
with the getHerokuAssignedPort method between main and class declaration

sample 
```java


public class App {
    static int getHerokuAssignedPort() {
        ProcessBuilder processBuilder = new ProcessBuilder();
        if (processBuilder.environment().get("PORT") != null) {
            return Integer.parseInt(processBuilder.environment().get("PORT"));
        }
        return 4567; //return default port if heroku-port isn't set (i.e. on localhost)
    }

    public static void main(String[] args) {

        port(getHerokuAssignedPort());
        staticFileLocation("/public");
    }

}
```

## Create Heroku App and heroku postgres db 

    - heroku login
    - heroku create name-of-app  : make sure to insert the app name you provided in your pom.xml file where name-of-app appears.
    - heroku addons:create heroku-postgresql:hobby-dev --app name-of-app  : to add a postgresql db to heroku app  : note name of DB given

3. Retrieve this db users credentials i.e. username and password by going to the heroku dashboard for your app,
   resources tab -> Click on your tier db in add-ons section -> in the screen it opens go to settings tab -> database credentials, view credentials -> save the user and password credentials.  :: This page also gives you a uri address for your db , save it somewhere 
4. Change the connection string to your db on heroku in your project java file(s).  to this example : remember to use your own credentials
   ```java
     String connectionString = "jdbc:postgresql://ec2-23-21-76-49.compute-1.amazonaws.com:5432/df2ubtmuhc32s7"; //!
     Sql2o sql2o = new Sql2o(connectionString, "stdhhdzdeynsis", "43a1b82999c0f772dbbd8f7602f0fa50c75b0c3e0f7b0c2caa36637a9569de10"); //!
    ```
   
##breakdown of connection string above
 - jdbc:postgresql:// -- Lets Java know we are using jdbc (java database connectivity) for Postgres
 - ec2-23-21-76-49.compute-1.amazonaws.com -- URI for the DB server
 - 5432 -- port number for the DB server
 - df2ubtmuhc32s7 -- route to the DB on the server
 - stdhhdzdeynsis -- username
 - 43a1b82999c0f772dbbd8f7602f0fa50c75b0c3e0f7b0c2caa36637a9569de10 -- password

##mvn heroku:deploy
5. Run : mvn heroku:deploy  : to deploy project without database
   The output should give you the url of your heroku app.
   
# recreate your db on heroku run :
    - heroku psql --app your-app-name : accessing heroku psql shell
do this : run the database queries with this heroku psql . i.e CREATE TABLE users .............

or you can : copy the schema of the your db : using         

          heroku pg:push local_database_name postgresql-corrugated-80870 --app your-app-name 
Where : 
 - postgresql-corrugated-80870 : name of db in heroku
 - local_database_name : name of db in your local environment 

:0 Happy Deploy

