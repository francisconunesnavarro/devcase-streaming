```
   _____       _                   _       
  / ____|     | |                 (_)      
 | (___  _   _| |_ __ _  ___       _  ___  
  \___ \| | | | __/ _` |/ __|     | |/ _ \ 
  ____) | |_| | || (_| | (__   _  | | (_) |
 |_____/ \__, |\__\__,_|\___| (_) |_|\___/ 
          __/ |                            
         |___/                              

                     ./work ./share ./relax 
```

# Sytac Development Challenge #

Congratulations to making it this far! The next step in the hiring process consist in a small coding assignment.  
We would like to know you better, and of course, see how you code!

> We have carefully crafted this challenge so that an averagely experienced developer should be able to complete somewhere between 4 and 6
> hours. If you find yourself spending considerably more time than this, please reach out to your Sytac HR and ask
> for help.

We appreciate that you may be doing this assignment next to your day job. Or you might have set a weekend aside to
complete this. Please be realistic and let our Sytac HR if you need more time. You are not judged on time it takes you to complete this assignment.

## Technical Requirements
 - [Docker](https://www.docker.com) installed locally
 - [Git](https://git-scm.com) installed locally
 - a Github account
 - a recent JDK installation (11+)
 - your favorite IDE
 
## Your task: the data harvester  🕵️‍♂️
Your task, should you choose to accept it, is to create a program that harvest real time usage information
about the users of `video streaming events' server`. The details of the platform and a list of the requirements are available down below.  
In order to fulfill the assignment, you will have to consume three streaming endpoints by running the video streaming events' server on your machine as described [here.](#how-to-run-the-video-streaming-events-server-locally)

## The video streaming events server 📺 ##

The `video streaming events server` exposes near real-time information regarding tv shows and movies streamed by users
on 3 streaming platform: Netflix, Amazon and Disney.  
Each of the endpoint exposes a stream of infinite events representing either the start of a streaming (`stream-started`) ,
the end of a streaming (`stream-ended`), liking a show (`show-liked`) or a problem during the streaming of a show
(`stream-interrupted`).

An example message from the stream is shown below:
``` 
id:664c318c-6f36-424f-adb9-56f7861aba27
event:show-liked
data:{
  "show": {
    "show_id": "s8105",
    "cast": "Marlon Brando, Bruce Willis",
    "country": "Netherlands, Belgium",
    "date_added": "April 7, 2017",
    "description": "A quarter-century later, this documentary relocates the male dancers who backed Madonna on her 1990 Blond Ambition tour, as seen in \"Truth or Dare.",
    "director": "Ester Gould, Reijer Zwaan",
    "duration": "85 min",
    "listed_in": "Documentaries, International Movies, LGBTQ Movies",
    "rating": "TV-MA",
    "release_year": 2016,
    "title": "Strike a Pose",
    "type": "Movie",
    "platform": "Netflix"
  },
  "event_date": "27-02-2023 03:20:17.111",
  "user": {
    "id": 116,
    "date_of_birth": "31/10/1970",
    "email": "gbeeton37@wikimedia.org",
    "first_name": "Guenna",
    "gender": "Female",
    "ip_address": "96.34.189.33",
    "country": "TH",
    "last_name": "Beeton"
  }
}
```
The timezone for each event is `Amsterdam (CET)` except for users from the countries listed below.
For these users, their local time zone is used to represent the date and time for every given event.
```  
Portugal(PT) = UTC (Lisbon time),
Canada(CA) = UTC -5 (Toronto time)
United States(US) = UTC -8 (Los Angeles time)
Russia(RU) = UTC +3 (Moscow time)
Indonesia(ID) = UTC +7 (Jakarta time)
China(CN) = UTC +8 (Shanghai time)
```
> Disclaimer: we have no affiliation with the named platforms, 
> the data used to generate these endpoints come from a publicly available datasource on the internet, 
> while the user data is randomly generated. No real user data is exposed by this server.  


### How to run the video streaming events server locally
You will need Docker installed on your machine. And then:

For Intel/AMD CPUs:  
`docker run -p 8080:8080 sytacdocker/video-stream-server:latest`

For Arm based CPUs (Apple m1/m2 chip):  
`docker run -p 8080:8080 sytacdocker/video-stream-server-arm:latest`

Once the server is running, you will have several endpoints available on your machine: [Netflix](http://localhost:8080/netflix), [Amazon](http://localhost:8080/amazon)
and [Disney](http://localhost:8080/disney) .  
All the endpoints are protected by username and password (basic auth):
```
username = sytac
password = 4p9g-Dv7T-u8fe-iz6y-SRW2
```
## Code review/evaluation criteria 👀

Your submission will be evaluated by Sytac on the following criteria:

+ Requirements coverage completeness
+ Correctness of the implementation
+ Decent test coverage
+ Code cleanliness
+ Efficiency of the solution
+ Careful choice of tools and data formats
+ Use of production-ready approaches

There are bonus points that you *may* do. These are provided as an inspiration where you can show off, in case you
would be wondering how to impress us more.

## Mandatory Requirements 📜

Your application should run for 20 seconds or stop on the third occurence of a user with first name "Sytac" (without quotes) 
on any of the three platforms (not necessarily the same platform).

During this period, you will have to collect all the events produced by all 3 platforms and for each 
user encountered, retrieve:
+ their user id
+ their name and surname
+ their age
+ all the events that they executed
+ on which platform the events occurred
+ the show titles
+ the first person in the cast for each show, if present
+ the show ids
+ at what time every event occurred, using Amsterdam (CET) timezone and `dd-MM-yyyy HH:mm:ss.SSS` format

and also:
+ the total number of shows released in the past 3 years (for any type of event)
+ for how long the 3 streams were consumed, in milliseconds


The data collected should then be printed on standard output or on a file or returned via an HTTP GET request.
This is up to you to decide.
You have freedom of choice also for the output format, as long as it can be easily interpreted by another program **and humans**.  


<details>
  <summary>Short on time? Here is a hint 💡</summary>


  ```kotlin
    "PT" -> "UTC"
    "CA" -> "America/Toronto"
    "US" -> "America/Los_Angeles"
    "RU" -> "Europe/Moscow"
    "ID" -> "Asia/Jakarta"
    "CN" -> "Asia/Shanghai"
  ```
</details>

<details>
  <summary>Here is another hint, for good luck 💡💡</summary>
  
   
Unfortunately, the server is bugged: from time to time the data returned is not well-formed.
</details>

## Bonus Requirements

> These requirements are optional points that you may implement if you are looking to impress us 🎩. 

#### Bonus #1: Stats 📊 
Along with the mandatory requirements, calculate the following statistics:

- the total number of `successful streaming events` per user:
  - a successful streaming event is a defined as (sub)sequence of a `stream-started`, followed right after temporally by a `stream-ended` for the same show id and platform. For Example:
    - `[{stream-started, s_01, netflix, 01_01_2000}, {stream-ended, s_01, netflix, 01_01_2001}]` => successful event  
    - `[{stream-started, s_01, netflix, 01_01_2000}, {stream-ended, s_01, amazon, 01_01_2001}]` => nothing  
    - `[{stream-started, s_01, netflix, 01_01_2000}, {stream-ended, s_02, netflix, 01_01_2001}]` => nothing
    - `[{stream-started, s_01, netflix, 01_01_2000}, {show_liked, s_01, netflix, 01_06_2000}, {stream-ended, s_01, netflix, 01_01_2001}]` => nothing  
    - `[{stream-ended, s_01, netflix, 01_01_2000}, {stream-started, s_01, netflix, 01_01_2001}]` => nothing

- what is the percentage of started streams for Netflix (over the total of the events for the Netflix platform)
- how many users from China started a stream on more than one streaming platform


#### Bonus #2: Graal VM'er ⚡

Your solution can be compiled and ran as a Graal VM native executable.

#### Bonus #3: Cloud Native application ☁️

+ The application is deployed to a public cloud provider (Azure, AWS, GCP or other)
  - It must be designed so that it is possible to call the data harvesting on demand and retrieve the results at a latter stage.  
    - For example: the app will provide a POST endpoint to start the harvesting and a GET endpoint to retrieve the data.
  - Your deliverable must include instructions on how to build it and deploy it

## Delivery format 🚚 ##

Please provide the code as a **Maven** or **Gradle** project.
You are free to choose any language/framework/library that runs on the JVM and fits with the purpose
of the application. Our preference is for **Kotlin** or **Java** languanges.  
Markdown instructions on how to run the program are always appreciated.

## How to work with GitHub 😼
You are assigned to your own private repository. Please create a feature branch and **do not commit on master**.
When the assignment is completed, please **create a pull request** on the master of this repository,
which will automatically notify your contact person about the completion of your delivery.


# Questions ❓

If you have questions regarding the development, reach out to the Sytac recruiter that is taking care of your hiring process. 
They will rely the message to the most suitable Sytac technical officer that will answer your queries in a timely manner.

