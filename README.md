Download Link: https://assignmentchef.com/product/solved-cs2106-lab-3-process-communication-and-synchronization-in-unix
<br>
There are <strong>three exercises </strong>in this lab. The purpose of this lab is to learn about process communication and synchronization in Unix-based OS. Due to the difficulty of debugging concurrent programs, the complexity of this lab has been adjusted accordingly. Hence, you should find the required amount of coding “minimal”. Most likely you will spend more time on understanding the various mechanisms than on the actual programming effort.




General outline of the exercises:

<ul>

 <li>Exercise 1: Understanding POSIX shared memory.</li>

 <li>Exercise 2: Understanding POSIX semaphores.</li>

 <li>Exercise 3: Using shared memory and semaphore to solve a synchronization problem.</li>

</ul>




Note: the last 3 pages of this document contain short descriptions on the essential library calls. You can consider not to print them to save paper.




<strong>2.1 Exercise 1 </strong><strong><u>[Lab Demo Exercise]</u></strong>




In this exercise, we are going to use <strong>POSIX shared memory</strong> mechanism to solve a simple problem. As a recap of lecture 5, the basic steps of using shared memory mechanism in general:

<ol>

 <li>Create a shared memory region</li>

 <li>All process intended to communicate with each other <strong>attach </strong>the shared memory region to their own memory space</li>

 <li>Read/Write to the shared memory region for communication</li>

</ol>




The major shared memory library calls are listed in the appendix A for your reference.

<strong>             </strong>

<strong><u>Shared Memory Mechanism Demonstration</u> </strong>




Under the “<strong>demo</strong>” subdirectory in “<strong>ex1″ </strong>directory, use the “<strong>make</strong>” command to compile the three sample programs: <strong>shdMemAndFork.c</strong>, <strong>shdMemServer.c</strong> and <strong>shdMemClient.c</strong>.




The program <strong>shdMemAndFork</strong>.c shows one way of connecting a parent process with its child process. You can run it by using the “<strong>shdMem</strong>” executable. Here’s a general description of the program:




<table width="507">

 <tbody>

  <tr>

   <td width="507"><strong>Parent Process: </strong></td>

  </tr>

  <tr>

   <td width="507">1.     Create and attach the shared memory region.2.     Spawn a child using <strong>fork() </strong>•       Useful trick: Shared memory region created and attached <strong>before </strong><strong>fork()</strong> will remain functional after <strong>fork()</strong>. Hence, the parent and child still share the memory region after <strong>fork()</strong>.3.     The shared memory region is treated as an <strong>integer array </strong>in this program.The line<strong>int* Array = (int*) shdMemRegion; </strong>points <strong>Array</strong> to the starting address of the shared memory region. Afterwards, we can treat <strong>Array</strong> as a normal integer array. The <strong>Array[]</strong> is used as follows:•       <strong>Array[0]</strong>: used for the parent process to indicate the values are ready for the child.•       <strong>Array[1]</strong> and <strong>Array[2]:</strong> 2 values “passed” to the child.•       <strong>Array[3]:</strong> used by the child process to indicate that the values have be overwritten and ready for the parent process to read.4.     Parent writes <strong>1234</strong> and <strong>5678</strong> to <strong>Array[1]</strong> and <strong>[2]</strong> respectively.5.     Parent writes <strong>9999</strong> to <strong>Array[0]</strong> to indicate the values are ready.6.     Parent repeatedly checks <strong>Array[3</strong>] for “<strong>1111</strong>” to see whether the child process is done•       This is one crude way to synchronize parent with child process.7.     Parent found <strong>1111</strong> in <strong>Array[3]</strong> and reads <strong>Array[1]</strong> and <strong>[2]</strong> to see the updated values.</td>

  </tr>

 </tbody>

</table>




<table width="507">

 <tbody>

  <tr>

   <td width="507"><strong>Child Process: </strong></td>

  </tr>

  <tr>

   <td width="507">1.     Spawned by the <strong>fork()</strong> system call at Step 2 of Parent Process2.     Repeatedly checks <strong>Array[0]</strong> for the value <strong>9999</strong>, which indicates the parent is done with writing.•       See Step 4 and 5 of Parent3.     Reads the value of <strong>Array[1]</strong> and <strong>[2].</strong>4.     Change the <strong>Array[1]</strong> and <strong>[2]</strong> to <strong>4321</strong> and <strong>8765</strong> respectively5.     Change <strong>Array[3]</strong> to <strong>1111</strong> to indicate the writing is done.•       See Step 6 of Parent</td>

  </tr>

 </tbody>

</table>

As an alternative, the two files <strong>shdMemServer.c</strong> and <strong>shdMemClient.c</strong> are provided to demonstrate communication between two independent processes with no parent-child relationship. The two files contain essentially the same code as the parent and child process in the previous code with the omission of <strong>fork()</strong> system call.




The key difference is that the client (which plays the child process role) has to attach the shared memory region “manually” using the memory region id. To run the program, you can do the following:




Use two terminal windows (as shown during week 5 lecture):

<ul>

 <li>In terminal A, run server program by using the executable “<strong>shdMemServer</strong>”.</li>

 <li>Take note of the memory region id printed on screen.</li>

 <li>In terminal B, run client program by using the executable “<strong>shdMemClient</strong>”.</li>

 <li>Enter the memory region id when prompted.</li>

 <li>Observe the results.</li>

</ul>




You can also use one single terminal window:

<ul>

 <li>Run the server program in background by “<strong>shdMemServer &amp;</strong>”.</li>

 <li>Take note of the memory region id printed on screen. o Run client program by using the executable “<strong>shdMemClient</strong>”.</li>

 <li>Enter the memory region id when prompted.</li>

</ul>




<strong><u>Your task for exercise 1:</u> </strong>




Write a <strong>single program </strong><strong>ex1.c </strong>(i.e. use the <strong>shmAndFork.c</strong> approach) to spawn a single child process then perform the task below:




<table width="507">

 <tbody>

  <tr>

   <td width="507"><strong>Parent Process: </strong></td>

  </tr>

  <tr>

   <td width="507">1.     Ask the user for the size of integer array, <strong>S</strong>.2.     Ask the user for the starting value to be placed in this array, <strong>V</strong>.3.     Store <strong>V</strong> in <strong>A[0]</strong>, <strong>V+1</strong> in <strong>A[1]</strong>, ……, <strong>V+S-1</strong> in <strong>A[S-1] </strong>•       The values in the array<strong> A[]</strong> should be communicated to the child process via shared memory region.4.     Let the child process to sum up the first halve of the shared array, i.e. <strong>A[0]</strong> to <strong>A[(S/2)-1</strong>].5.     Sum up the second halves of the shared array, <strong>A[(S/2)]</strong> to <strong>A[S-1]</strong>6.     Wait for the child to finish summing and report the following:•       Partial sums from the parent and child processes.•       The final sum.</td>

  </tr>

 </tbody>

</table>




<table width="507">

 <tbody>

  <tr>

   <td width="507"><strong>Child Process: </strong></td>

  </tr>

  <tr>

   <td width="507">1.     Wait for the parent to finish populating the shared array <strong>A.</strong>2.     Sum up the first halve of the shared array, i.e. <strong>A[0]</strong> to <strong>A[(S/2)-1].</strong>3.     Report the partial sum to parent process.</td>

  </tr>

 </tbody>

</table>

Sample Execution Session: (User input in <strong>bold</strong> font)




<table width="507">

 <tbody>

  <tr>

   <td width="233">Enter Array Size:<strong> 10 </strong>Enter Start Value:<strong> 1  </strong></td>

   <td width="274">//Shared Array = {1, 2, 3, …, 8, 9, 10}</td>

  </tr>

  <tr>

   <td width="233">Parent Sum = 40 <strong>       </strong></td>

   <td width="274">//6+7+8+9+10</td>

  </tr>

  <tr>

   <td width="233">Child Sum = 15 <strong>        </strong>Total = 55 </td>

   <td width="274">//1+2+3+4+5</td>

  </tr>

 </tbody>

</table>




<strong>Important Note: </strong>

<strong> </strong>

Please ensure you use shared memory to pass the values between parent and child.

<strong>Any other mechanism will not be accepted.</strong> J

<strong>             </strong>

<h2>2.2 Exercise 2</h2>

<strong> </strong>

In this exercise, we are going to learn about <strong>POSIX semaphore</strong>. As discussed in lecture, semaphore is a simple yet powerful synchronization mechanism. The POSIX semaphore is one possible implementations under Unix, other popular choice include <strong>phtread_mutex</strong> (semaphore implementation for <strong>pthread</strong> library).




The major semaphore library calls are listed in the <strong><em>appendix B</em></strong> for your reference.




<strong><u>Semaphore Mechanism Experiments</u> </strong>




Under the “<strong>demo</strong>” subdirectory in “<strong>ex2″ </strong>directory, there are 2 sample programs: <strong>semaphore_example.c</strong> and <strong>semaphore_exampleImproved.c</strong>. You can similarly use the “<strong>make</strong>” command to compile them.




In <strong>semaphore_example.c</strong>, pay special attention to semaphore allocation in the shared memory region. Note the step where the shared memory region is type casted as (<strong>sem_t*</strong>), which allows the region to be used as a semaphore structure.




The program consists of a very simple interaction between a parent and child process. The parent and child process will prints out three occurrences of character ‘<strong>p</strong>’ and ‘<strong>c</strong>’ respectively. Without any synchronization, you can see interleaving patterns, e.g.

<strong> </strong>

<table width="480">

 <tbody>

  <tr>

   <td colspan="2" width="480"><strong>Examples of interleaving pattern in output</strong></td>

  </tr>

  <tr>

   <td width="213"><strong>p c c p p c </strong></td>

   <td width="267"><strong>p c p c p c </strong></td>

  </tr>

 </tbody>

</table>

<strong> </strong>

<strong> </strong>

Suppose we want to force the processes to print out the character consecutively, i.e. <strong> </strong>

<table width="480">

 <tbody>

  <tr>

   <td colspan="2" width="480"><strong>The only two correct consecutive patterns in output</strong></td>

  </tr>

  <tr>

   <td width="213"><strong>p p p c c c  </strong></td>

   <td width="267"><strong>c c c p p p  </strong></td>

  </tr>

 </tbody>

</table>




This can be easily accomplished by using a binary semaphore to “protect” the whole for-loop in the parent or child process. In the same program, the semaphore is already allocated and initialized. Place the <strong>sem_wait()</strong> and <strong>sem_post()</strong> at appropriate locations to get the desired behavior.

The <strong>semaphore_exampleImproved.c</strong> is simply a modularized version of the <strong>semaphore_example.c</strong>.

<strong> </strong>

<strong><u>Your task for exercise 2:</u> </strong>




Firstly, we have repackaged the semaphore functions seen in the demo programs into a set of function wrappers. This allows us to use the semaphore without worrying about how to allocate them in the share memory region etc. To be more general, the functions <strong>allocate array of semaphores instead of a single semaphore</strong>. The functions can be found at the top of <strong>ex2.c</strong>.




Consider this problem: Two processes (parent and its child) cooperate to fill a shared array of size <strong>500,000</strong>. The parent process will fill the array using the value <strong>1111</strong>, while the child process uses <strong>9999</strong>. Each process should produce <strong>250,000</strong> values individually. However, the order in which the process fill in the shared array is not relevant (i.e. the <strong>9999</strong> and <strong>1111</strong> in the array need not follow any specific pattern).




To facilitate the production, a shared array <strong>sharedArray</strong> of <strong>500,00<u>1</u></strong> values is allocated. The first location <strong>sharedArray[0]</strong> stores the index of the next free location in the array. So, the parent and child process should perform the following for <strong>250,000</strong> times:

<ol>

 <li>Read the index from <strong>sharedArray[0]. </strong></li>

 <li>Produce the value (<strong>9999</strong> or <strong>1111</strong>) to the free slot.</li>

 <li>Increase the index in <strong>sharedArray[0]. </strong></li>

</ol>




The last part of the parent process consists of a simple auditing code, which goes through the shared array and checks the number of occurrences of <strong>1111</strong> and <strong>9999</strong>. In a correctly coded program, you should see this result at the end of execution:







<strong>  Audit Result: P = 250000, C = 250000, N = 0 </strong>







<strong>P</strong> and <strong>C</strong> represent the occurrences of <strong>1111</strong> and <strong>9999</strong> respectively, which should be exactly <strong>250,000</strong> each in a correct program. <strong>N</strong> represents occurrences of any other value, which <strong>should be zero</strong> in a correct program.




Please note that:

<ul>

 <li>You should not change the allocation or layout of the shared array in anyway.</li>

 <li><strong>You should leave the auditing code untouched</strong> to facilitate our testing.</li>

 <li>You must use semaphore to solve this task:</li>

</ul>

o    Semaphore should be used to protect critical code only. If your critical section contains more operations than absolutely necessary, you may be penalized.

<h2>2.3Exercise 3</h2>

<strong> </strong>

This exercise makes use of both share memory region and semaphore to solve a synchronization problem.




Consider a modern age implementation of the classic <strong>mancala game: </strong>










The new mancala game supports <strong>5 players or more </strong>and has as many “plates” as the number of players. In each of the plates, there are a number of randomly placed <strong>red, green </strong>and <strong>blue </strong>color beads.  Each player has an id [0 to number of players -1] and each plate is similarly numbered. A player has <strong>only one</strong> simple move:

<ul>

 <li>Choose beads of a particular color in his/her plate (i.e. plate with the same id as his/her id).</li>

 <li>Move 1 to 5 beads to <strong>next plate</strong> (i.e. plate with the id+1, wraps around). As each plate can have at most 99 of each of the color beads, the player will always respect the limit (i.e. move less or none if the target plate is almost full / full).</li>

</ul>




The entire game logic has already implemented for you J. Your job is just to make sure the players don’t mess up each other’s plate…..




Key notes of the game logic implementation:

– Each “plate” is simulated as a 6-digit integer. As each type of color beads cannot exceed 99, we store the blue color beads as the last two digits, green as the middle two digits and green as the first two digits. e.g. “<strong>12</strong><strong>34</strong><strong>56</strong>” means 12 red color beads, 34 green color beads and 56 blue color beads.




<strong>             </strong>

<strong><u>Your task in exercise 3:</u> </strong>




Compile the given <strong>ex3.c</strong> and try it out. Remember that you need a “-<strong>lpthread</strong>” flag for the semaphores library. The program expects 3 command line arguments:

<h2>     a.out [random seed] [no. of players] [no. of rounds]</h2>




<ul>

 <li><strong>[Random seed]</strong> is used to setup the random number generator. Using the same seed should give you the same setup between runs. – <strong>[No. of Players]</strong> should be larger or equal to 5.</li>

 <li><strong>[No. of Rounds]</strong> indicates how many moves per player are to be performed.</li>

</ul>




The program creates <strong>[No. of Players]</strong> concurrent processes and let them access a shared mancala. Each player process will perform the movement of color beads <strong>[No. of Rounds]</strong> times. At the end of the program, two important checks are performed:

<ol>

 <li>Whether the total number of {red, green, blue} beads in the mancala remains the same. This should be intuitively true, as beads are move around but never removed / added.</li>

 <li>The maximum number of simultaneous players at any point in time. This shows you how many process played “at the same moment in time”.</li>

</ol>




If you run the program now with random seed <strong>12345</strong> and <strong>5</strong> players, you can see that the verification actually fails when the number of rounds is large.

[Note: round number is system dependent, around 200 and above in the lab machine, more than 70000 rounds on Solaris J]

[Note<sup>2</sup>: Timing dependent! The same parameters may give different behavior!].




Below is an example of a failed run:







You can see that we started with <strong>[101]</strong> red beads and somehow ended up with <strong>[102]</strong> red beads! Similar issue plagues the green color beads too. In some runs, color beads can disappear into thin air!




So, your job is very simple, <strong>fix the synchronization </strong>between the processes so that we will always have consistent game state before and after. Note that you <strong>can only modify the program at the places marked with a “//TODO” comment.</strong>







The marking of this question follows a tiered system:







<strong>Please note that we only consider higher tier scoring if your code satisfy ALL lower tier(s) requirements. </strong>For example, an incorrect program receives <strong>0 regardless of how many simultaneous players supported.</strong> Similarly, if your code does not allow the maximum parallelism (tier 2), we will not test beyond 5 players (tier 3).




Hints:

<ul>

 <li>Identify <strong>the type of synchronization problem first</strong>.</li>

 <li>The solution is very short, you need around 10 lines of new code.</li>

 <li>Try the <strong>simplest solution first</strong>.</li>

</ul>







<strong> </strong>

<strong><u>Bonus Round!</u> </strong>




When working with multi-process programs (like lab 2 and this lab), there are two irritating problems:

<ol>

 <li>When the parent process is terminated with <strong>[ctrl-c]</strong>, child processes may be still running around.</li>

 <li>Similarly, resources allocated (e.g. shared memory, semaphores etc) may not be cleared up properly if the program dies a “unnatural death”.</li>

</ol>




Fortunately, (a) is already handled automatically by terminal. When the [ctrl-c] signal (i.e. SIGINT) is send to the foreground process, the signal will be relayed to the <strong>entire process group (i.e. all child spawned). </strong>So, we only need to worry about (b).




Let’s utilize what you have learned about <strong>unix</strong> <strong>signal </strong>and add the following functionality:

– When the parent process received the SIGINT signal, it will monitors the termination of all child processes and exit <strong>only when all child processes are terminated.</strong>




Use the same reporting format as the following sample run:







The <strong>[Ctrl-C]</strong> caused a “<strong>^C</strong>” be printed on screen. The “<strong>PID[XXXXX] Ended</strong>” is printed by each of the child process, while the “<strong>t[XXXX] terminated</strong>.” is reported by the parent process. The last “<strong>PID[2784] Ended</strong>.” belongs to the parent process.

<strong> </strong>

<strong>Notes: </strong>

<ul>

 <li>The bonus score is only considered <strong>if you meet at least the tier 1 correctness requirement. </strong></li>

 <li>You can use this functionality to clean up the system resources properly, e.g. detach and delete share memory regions etc. However, due to the many ways in which you can allocate system resources, we <strong>will not check </strong>on the cleanup code.</li>

 <li>Make sure the bonus segment does not affect your main solution in anyway. It is probably easier to experiment and learn in a separate program before you place it in the main solution code.</li>

 <li>You need to read up on the “<strong>signal()</strong>” system calls.</li>

</ul>