# Scheduler Java App
Creating a scheduler with Java

Tools:
1. Quartz (version 2)
2. Maven
3. Java 17

## Creating a Maven Project
1. Create a folder to store the project
2. Run the command ``` mvn archetype:generate ``` to see the archetype options shared by Maven
3. Choose the default ```maven-archetype-quickstart```: *option 1947* (when this README have been written)
4. Continue choosing the *default options* until the end 
5. Define the *groupId* and *artifactId* for your project

## Including the Quartz dependency
Link: https://mvnrepository.com/artifact/org.quartz-scheduler/quartz/2.3.2
```
<!-- https://mvnrepository.com/artifact/org.quartz-scheduler/quartz -->
<dependency>
    <groupId>org.quartz-scheduler</groupId>
    <artifactId>quartz</artifactId>
    <version>2.3.2</version>
</dependency>
```

## Creating an implementation of the Job Interface from Quartz
1. Add the Interface to the new class: ``` public class JobImpl implements Job ```
2. Shortcut: **CTRL** + **.** to import the interface *org.quartz.Job* and to override its method *execute*
3. This method will be responsible for all the logic of the scheduling process, because it is the process that will run in the configured frequency.
```
package br.com.karlbill.scheduler;

import org.quartz.Job;
import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;

public class JobImpl implements Job {

    @Override
    public void execute(JobExecutionContext context) throws JobExecutionException {
        System.out.println("Put the logic of the scheduling process here");        
    }    
}
```

## Simple Trigger x Cron Trigger 
Cron Trigger is a UNIX pattern to set the frequency time of the scheduler
> Example: 10 12 * * * /test.js /*it sets the frequency time of the job to run everyday after 12:00 p.m, in an interval of 10 minutes */

Simple Trigger is a newer way to express the Cron Trigger in a friendly way for humans.

### Implementing the Trigger, the Job and the Scheduler in the main method
We will use the Trigger class from org.quartz package and instantiate a new Trigger that is going to use a SimpleScheduleBuilder to set the time parameters in a friendly way:
```
Trigger trigger = TriggerBuilder.newTrigger()
                            .withIdentity("TRIGGER", "GROUP")
                            .withDescription("SIMPLE-TRIGGER")
                            .withSchedule(SimpleScheduleBuilder
                                            .simpleSchedule()
                                            .withIntervalInSeconds(2)
                                            .repeatForever()
                                        ).build(); 
```
After created the trigger, we have to create the job that will be instantiated based on the JobImpl that we have created:
```
JobDetail jobImpl = JobBuilder.newJob(JobImpl.class)
                                        .withIdentity("JOB-IMPL", "GROUP")
                                        .withDescription("JOB RUNNING EACH 2 SECONDS")
                                        .build();
```
With the job and the trigger created, now we have to create a Scheduler that is going to receive the job and the trigger that we set before:
```
Scheduler scheduler = new StdSchedulerFactory().getScheduler();
scheduler.start();
scheduler.scheduleJob(jobImpl, trigger);
```

## Running the application
The final result of the running app is shown below:

![image](https://user-images.githubusercontent.com/39681960/203430963-f112afd7-7943-4ec4-a2e9-e74863e920e0.png)

> See that *JobImpl* object is running in an interval of 2 seconds. So, you could change the overrided *execute* method of JobImpl class to see something interesting running there.

**P.S: Replacing the SimpleScheduleBuilder by a CronScheduleBuilder**
- If we want to use the UNIX pattern of scheduling, we have to replace the *SimpleScheduleBuilder* by a *CronScheduleBuilder* :
```
Trigger trigger = TriggerBuilder.newTrigger()
                            .withIdentity("TRIGGER", "GROUP")
                            .withDescription("SIMPLE-TRIGGER")
                            .withSchedule(CronScheduleBuilder.cronSchedule("0/2 * * * * ?")
                                        ).build(); 
```

> The same result is obtained as before.








