# Assignment 3 - Complete Documentation

**Student Name**: [Your Full Name]  
**Student ID**: [Your ID]  
**Date Submitted**: [Submission Date]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [Paste your personal Gmail Google Drive link here]

**Video filename**: `[YourStudentID]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

### Entry 2 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

### Entry 3 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

### Entry 4 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

### Entry 5 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

[Your answer here - 4-6 sentences with code examples]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

[Your answer here - explain your implementation choices]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

[Your answer here - reference try-finally blocks, lock ordering, etc.]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

[Your answer here - explain coarse-grained vs fine-grained locking, independence of counters, concurrency implications. Show understanding of when to use each approach. 5-8 sentences expected.]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 

**Why they need protection**: 

**Synchronization mechanism used**: 

**Code snippet**:
```java
public static void incrementContextSwitch() {
        lock.lock(); //task 1: protect entire method with lock
        try {
        contextSwitchCount++;
    } finally {
            lock.unlock();
        }
    }
```

**Justification**: 

---

### Critical Section #2: Execution Log

**What resource**: 

**Why it needs protection**: 

**Synchronization mechanism used**: 

**Code snippet**:
```java
public static void logExecution(String message) {
        lock.lock(); //task 1: protect entire method with lock
        try {
            // TODO: Protect this critical section with a lock
            // RACE CONDITION: ArrayList is not thread-safe!
        executionLog.add(message);
   } finally {
            lock.unlock(); 
        }
    }
```

**Justification**: 

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 

**Number of permits and why**: 

**Where implemented**: 

**Code snippet**:
```java
public void run() {
        // --- TASK 3 CHANGE: Acquire CPU semaphore before executing ---
        try {
            SharedResources.cpuSemaphore.acquire();
// ... [CPU Execution]
catch (InterruptedException e) {
            System.out.println(Colors.RED + "Thread interrupted while waiting for CPU." + Colors.RESET);
        } finally {
            // TASK 3 CHANGE: Release CPU semaphore here ---
            SharedResources.cpuSemaphore.release();
```

**Effect on program behavior**: 

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running the program multiple times to verify consistent results and ensure the absence of race conditions or data corruption

**Testing procedure**: 
Compiled the SchedulerSimulationSync.java file using javac.

Ran the program 5 consecutive times using the same Student ID (445050091)

Monitored the console for any ConcurrentModificationException or InterruptedException

Verified that the Total Completed Processes matched the number of processes created every single time
```bash
- 0 -
Total Context Switches: 26
Total Completed Processes: 14
Total Waiting Time: 494067ms
Average Waiting Time: 35290ms
Total log entries: 52
- 1 -
Total Context Switches: 26
Total Completed Processes: 14
Total Waiting Time: 493757ms
Average Waiting Time: 35268ms
Total log entries: 52
- 2 -
Total Context Switches: 26
Total Completed Processes: 14
Total Waiting Time: 493914ms
Average Waiting Time: 35279ms
Total log entries: 52
- 3 -
Total Context Switches: 26
Total Completed Processes: 14
Total Waiting Time: 494185ms
Average Waiting Time: 35298ms
Total log entries: 52
- 4 -
Total Context Switches: 26
Total Completed Processes: 14
Total Waiting Time: 494261ms
Average Waiting Time: 35304ms
Total log entries: 52




**Results**: 
The execution was strictly sequential; no two processes ever printed "executing quantum" at the exact same timestamp, proving the Semaphore was holding threads back correctly

**Why synchronization is necessary**: 
Even if the program seems to work once without it, synchronization is required to prevent Race Conditions

Shared Counters: Without ReentrantLock, two threads might perform count++ simultaneously. If count is 5, both might read 5, both increment to 6, and write back 6. One increment is lost, and the final statistics would be mathematically wrong.

The Log (ArrayList): Without protection, multiple threads calling executionLog.add() would likely cause a ConcurrentModificationException because the internal pointer of the list would be updated by two threads at once, corrupting the memory of the list.

CPU Access: Without the Semaphore, all process threads would start at the exact same time, printing their progress bars simultaneously and creating a jumbled, unreadable mess in the console.

**Conclusion**: 
The implementation of Mutex Locks ( ReentrantLock ) and Binary Semaphores successfully eliminated the risks of data corruption and race conditions. The program is now "Thread-Safe," providing reliable and predictable behavior regardless of how many threads are competing for resources.

---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException and other thread related crashes

**Testing procedure**:
-I increased the complexity of the simulation by using a high number of processes (as determined by the student ID seed) to ensure multiple threads were frequently calling executionLog.add().

-I observed the console output during the entire execution of the 5 test runs to see if the JVM threw any exceptions

**Results**:
In all 5 test runs, zero exceptions were thrown. The program moved smoothly from the "Scheduler Starting" phase to the "Final Statistics" phase without crashing. The "Total log entries" (52) remained constant across every run, confirming that every single attempt to write to the log was successful and synchronized.

**What this proves**: 
This proves that the ReentrantLock successfully created a Mutual Exclusion zone around the ArrayList. By locking the list before adding a message and unlocking it afterward, we prevented the "Race Condition" that occurs when two threads try to modify the internal structure of an ArrayList at the same time. Without this lock, the program would have likely crashed with a ConcurrentModificationException as soon as two processes tried to log a "yield" or "start" event simultaneously.
---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: 

**Actual values**: 

**Analysis**: 

---

### Test 4: Different Scenarios
**Scenario tested**: [e.g., different time quantum, more processes, etc.]

**Purpose**: 

**Results**: 

**What I learned**: 

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[6-8 sentences about key concepts, challenges, insights]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 

**Example 2**: 

---

### How I would explain synchronization to others:

[Explain to someone who just finished Assignment 1 - use simple terms and analogies]

---

## Part 6: GitHub Repository Information

**Repository URL**: 

**Number of commits**: 

**Commit messages**: 
1. 
2. 
3. 
4. 

---

## Summary

**Total time spent on assignment**: 

**Key takeaways**: 
1. 
2. 
3. 

**Most challenging aspect**: 

**What I'm most proud of**: 

---

**End of Documentation**
