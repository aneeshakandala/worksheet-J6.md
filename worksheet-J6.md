## 1.Compile the two files below and run the main method in the SimpleThreadExample.

```
$ javac *.java
$ java SimpleThreadExample
/* SimpleThreadExample.java */
public class SimpleThreadExample {

    public static void main(String[] args) {
    
        
        Messages msg = new Messages();
        Thread morningThread = new GreetingsThread(msg, 0);
        Thread afternoonThread = new GreetingsThread(msg, 1);

        morningThread.start();
        afternoonThread.start();
        //MARK
        
    }
}
/* Messages.java */
public class Messages {
    public String morning;
    public String afternoon;
    public String evening;
    public String night;

    public Messages() {
      morning = "Good morning!";
      afternoon = "Good afternnon!";
      evening = "Good evening!";
      night = "Good night!";
    }

    public String getMessage(int choice) {
      switch(choice) {
         case 0:
           return this.morning;
         case 1:
           return this.afternoon;
         case 3:
           return this.evening;
         case 4:
           return this.night;
         default:
           return "";        
      }
    }
 }
/* GreetingsThread.java */
public class GreetingsThread extends Thread {

    private Messages msg;
    private int choice;
    public GreetingsThread(Messages msg, int choice) {
        this.msg = msg;
        this.choice = choice;
    }

    public void run() {
        System.out.println("Started the " + msg.getMessage(choice) + " thread...");
            
        //print 10 times
        for (int i = 0; i < 10; i++) {
            System.out.println(msg.getMessage(choice));
        }

        System.out.println("Exiting the " + msg.getMessage(choice) + " thread...");
    }
}
```
Then:

Run the program a few times. Describe the output of this program. Is it consistent?

- The output is somewhat consistent. The output everytime is not the same. Sometimes, the output print out the "Good afternnon!" (with afternnon spelled that way) thead first and then starting the "Good morning!" and outputing that before exitting both. Other times, the "Good morning!" thread is outputted first before the "Good afternnon!" thread. These two different options are outputting everytime, but the order differs.
- For reference, the "Good morning!" thread outputs the phrase "Good morning!" 10 times, while the "Good afternnon!" thread outputs "Good afternnon!" 10 times. 

Draw a memory diagram of the program at MARK


## 2. Now let’s modify GreetingsThread to add a Thread.sleep() in the run() method. Recompile the GreatingThread class as below.

Run the program multiple times. Does the output change in any way? Does one thread always finish first, or does the order change?

```
/* GreetingsThread.java */
public class GreetingsThread extends Thread {
    private Messages msg;
    private int choice;

    public GreetingsThread(Messages msg, int choice) {
        this.msg = msg;
        this.choice = choice;
    }

    public void run() {
        System.out.println("Started the " + msg.getMessage(choice) + " thread...");

        for (int i = 0; i < 10; i++) {
            System.out.println(msg.getMessage(choice));

            try {
                // Sleep for 1 second (1000 milliseconds)
                Thread.sleep(1000); 
            } catch (InterruptedException e) {
                System.out.println("Interrupted while sleeping...");
            }
        }

        System.out.println("Exiting the " + msg.getMessage(choice) + " thread...");
    }
}
```
Yes, the output does change everytime the code is ran. Sometimes the "Good morning!" thread is exitting first, othertimes the "Good afternnon!" thread is exitting first. In similar fashion, sometimes the "Good morning!" thread is starting first and vice versa. The output also changes. There are times where "Good morning!" is printed and "Good afternnon!" prints back and forth, othertimes the same back and forth printing is done with "Good afternoon!" printed first and then "Good morning!". Additionally, sometimes "Good morning!" is printed two times in a row before the back and forth motion continues, and other times the same thing occurs with "Good afternoon!" printing two times in a row. 

## 3. Now let’s add a System.out.println() at the end of the main method. Recompile the program with this addition, continuing from above. Explain how it is possible that the main method is complete but the program is still producing output.

Essentially, it is possible that the main method is complete but the program is still prodcuing output because the main method does not wait for the other threads, in this case morningThread and afternoonThread to complete before the main method inevitably exits. 

## 4. Finally let’s add a thread.join() to join the morningThread for 5 seconds before starting the afternoonThread. Recompile and rerun. Then describe the output of this program. Explain how attempting to join the first thread for 5 seconds affects the output of this program.

```
/* SimpleThreadExample.java */
public class SimpleThreadExample {
    public static void main(String[] args) {
        Messages msg = new Messages();
        Thread morningThread = new GreetingsThread(msg, 0);
        Thread afternoonThread = new GreetingsThread(msg, 1);

        morningThread.start();

        try {
            System.out.println("Joining the morning thread for 5 seconds...");
            morningThread.join(5000);
        } catch (InterruptedException e) {
            System.out.println("Interrupted while joining a thread...");
        }

        afternoonThread.start();

        System.out.println("Main method exiting...");
    }
}
```
It seems as if the output is going on for a little bit longer than it did in the previous questions. Attempting to join the first thread for 5 seconds affects the output of this program because it essentially means that the main method must wait 5 seconds for the morningThread to finish.

## 5. What does join() do compared to join(10)?

Join() waits until the target thread finishes, while join(10) waits about 10 milliseconds for the other thread to finihs.

## 6. What is the difference between isAlive() and join()? (note, no arguments to join.)

IsAlive() checks if the thread is "alive", meaning that it checks if it is running and returns true and otherwise false. Join() actually waits for the thread to finish. 

## 7. Compile the two files below and run the main method in the AnimalFootRace. Then describe the output of this program. Explain why the threads finish in the order that they do.

```
public class AnimalFootRace {
    public static void main(String[] args) {
        Thread tortoiseThread = new AnimalRacerThread("Tortoise", 5);
        Thread hareThread = new AnimalRacerThread("Hare", 20);
        Thread cheetahThread = new AnimalRacerThread("Cheetah", 50);

        System.out.println("On your marks, get set, go!");
        tortoiseThread.start();
        hareThread.start();
        cheetahThread.start();
    }
}
/* AnimalRacerThread.java */
public class AnimalRacerThread extends Thread {
    public static final int NUM_LAPS = 8;
    private String animalName;
    private int animalSpeed;

    public AnimalRacerThread(String animalName, int animalSpeed) {
        this.animalName = animalName;
        this.animalSpeed = animalSpeed;
    }

    @Override
    public void run() {
        for (int i = 1; i <= NUM_LAPS; i++) {
            System.out.println(animalName + " lap " + i);
            try {
                Thread.sleep(1000 / animalSpeed);
            } catch (InterruptedException e) {
                System.out.println("Interrupted while sleeping...");
            }
        }
        System.out.println(animalName + "Finished!");
    }
}
```
The output simulates a race, with three different threads representing different animals running in a race. The threads are set to ``` Thread.sleep(1000 / animalSpeed);``` before printing out a lap statement. This is done so that the lap statements are more realistic given each animal (i.e. Turtle is the slowest, turtle normally finishes last). 

## 8. Modify the AnimialFootRace main method. Add additional code to give the hare a head start, without modifying the animal speeds. Then compile the two files below and run the main method in the AnimalFootRace. Provide the modified code below. Then describe the output of this program. Explain why your modification worked or did not work.

Modified code:
```
public class AnimalFootRace {
public static void main(String[] args){
Thread tortoiseThread = new AnimalRacerThread("Tortoise", 5);
Thread hareThread = new AnimalRacerThread("Hare",20);
Thread cheatahThread = new AnimalRacerTHread("Cheetah", 50);

System.out.println("On your marks, get set, go!");
hareThreadstart()
try {
hareThread.join();
}
catch(InterruptedException e) {
System.out.println("Interreupted while joining");
}
cheetahThread.start();
tortoiseThread.start();

}
}
```

Output:
On your marks, get set, go!
Tortoise lap 1
Cheetah lap 1
Hare lap 1
Cheetah lap 2
Cheetah lap 3
Hare lap 2
Cheetah lap 4
Cheetah lap 5
Hare lap 3
Cheetah lap 6
Cheetah lap 7
Hare lap 4
Cheetah lap 8
Tortoise lap 2
Hare lap 5
CheetahFinished!
Hare lap 6
Hare lap 7
Hare lap 8
Tortoise lap 3
HareFinished!
Tortoise lap 4
Tortoise lap 5
Tortoise lap 6
Tortoise lap 7
Tortoise lap 8
TortoiseFinished!


I started the hare thread before the tortoiseThread and added a join() to it so that the main function would need to wait for hare to finish before running the other two threads. The output was successful: Cheetah finished first, then Hare, then turtle. 

## 9. 
```
/* AnimalFootRace.java */
public class AnimalFootRace {
    public static void main(String[] args) {
        Thread tortoiseThread = new AnimalRacerThread("Tortoise", 5);
        Thread hareThread = new AnimalRacerThread("Hare", 20);
        Thread dogThread = new AnimalRacerThread("Dog", 30);
        Thread cheetahThread = new AnimalRacerThread("Cheetah", 50);

        System.out.println("On your marks, get set, go!");
        tortoiseThread.start();
        hareThread.start();
        humanThread.start();
        cheetahThread.start();
    }
}
```
output:

On your marks, get set, go!
Dog lap 1
Tortoise lap 1
Hare lap 1
Cheetah lap 1
Cheetah lap 2
Dog lap 2
Cheetah lap 3
Hare lap 2
Dog lap 3
Cheetah lap 4
Dog lap 4
Cheetah lap 5
Hare lap 3
Cheetah lap 6
Dog lap 5
Cheetah lap 7
Dog lap 6
Hare lap 4
Cheetah lap 8
Dog lap 7
Tortoise lap 2
CheetahFinished!
Hare lap 5
Dog lap 8
DogFinished!
Hare lap 6
Hare lap 7
Hare lap 8
Tortoise lap 3
HareFinished!
Tortoise lap 4
Tortoise lap 5
Tortoise lap 6
Tortoise lap 7
Tortoise lap 8
TortoiseFinished!

I decided to add a dogThread, with the speed of 30. I thought that a dog would be a good animal because it is faster than a hare but way less fast than a cheetah. The output reports the winners in the following order, Cheetah, Dog, Hare, then Tortoise, which I believe is an accurate reflection of the animal's speed. 

## 10. The Singleton class below implements the Singleton pattern. The singleton pattern is a software engineering design pattern that restricts the instantiating of a class to a single instance. Review the Singleton class code below. Is it possible to create more than one instance of the Singleton class if two threads attempt to call the getInstance() method at the same time?

It is possible for more than one instance of the class to be created if two threads attempt to call the getInstance() method at the same time. This happens because the order that threads get to run is determined by the system compiler, not code. If two threads reach the if statement around the same time, the first thread might not have finished creating the instance before the second thread also checks the condition. Therefore, both threads could pass the conditional check and each create a separate instance of Singleton.

## 11. Review the modified thread safe Singleton class code below. Is it possible to create more than one instance of the Singleton class if two threads attempt to call the getInstance() method at the same time? How does the synchronized keyword affect the attempts to call the getInstance method from multiple threads at the same time?

It is not possible to create more than one instance of the Singleton class if two threads attempt to call the getInstance() method at the saem time. Here, only one instance of the Singleton class is able to be created. The keyword in getSingletonInstance() makes sure that only one thread can be inside the method at a time, which means that the first thread that enters creates the Singleton instance. Any other thread that tries to enter has to wait until the first one finishes. The other threads that enter the method won’t go into the if statement again, so it appears that no new instances will be made.

## 12. Review the ThreadRunner class code below. Compile and run the ThreadRunner main method. Describe the output. Does the execution halt? Does SimpleThreadTwo finish running? If not, why does SimpleThreadTwo get stuck in the while loop (hint: recall the compiler optimization example)?

This code prints statements like Simple thread one counter - 1 up to Simple thread one counter - 2000 using a for loop. After that, it prints Status flag changed to true in simple thread one. After this, the program gets stuck in an endless while loop in simpleThreadTwo. This happens because simpleThreadTwo never seems to notice that the flag was updated in simpleThreadOne. WaitCounter just keeps increasing without ever stopping.

## 13. Review the modified ThreadRunner class code below. Compile and run the ThreadRunner main method. Describe the output. Does the execution halt? Does SimpleThreadTwo finish running? If it does finish running, why does adding the volatile keyword before the boolean statusFlag change the behavior of the code compared to the previous question?

This code gives the same output as before, but it prints Start simple thread two processing and shows the value of waitCounter. Since we added the volatile keyword to statusFlag, this works out well. This means when statusFlag is set to true in simpleThreadOne, it updates right away in simpleThreadTwo. Therefore, the code can exit the while loop and print the final message instead of getting stuck.

## 14. A second price auction is an auction where the highest bidder only has to pay whatever the second highest bid was. For example if person A bids $1 and person B bids $2, then person B wins the auction, but only pays $1. Below are two classes that reflect this, SecondPriceAuction and Bidder.

Review the code below. What might happen if two threads call the makeBid method at the same time? How would you modify the code to protect the auction outcome?

A race condition can happen in makeBid. Making it synchronized solves this by forcing one thread to wait until the other finishes. This way, each Bidder thread can place its bid without interference from another Bidder. Declaring makeBid this way makes sure that that one Bidder has to finish before the next one can place their bid. 

