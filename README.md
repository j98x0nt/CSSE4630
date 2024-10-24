java c
CSSE4630 Assignment 2: Dynamic Analysis: MBT, PBT, Concolic testing for Rust
1    Introduction
In this assignment you will learn about:
• using the Rust testing framework to test a Rust library;
• measuring several kinds of test coverage, including statement coverage and mutationscore;
• using two black-box test generation techniques to make sure you are systematically testing a good range of the functional behaviour of the library (and we will measure the resulting
code coverage to see if it is higher).  The two black-box techniques are:
1.  simple model-based testing (MBT) based on finite-state machine models;
2. property-based testing (PBT) to check particular properties of your implementation;
• using a white-box test generation technique  (concolic testing) to achieve  100% branch coverage of a critical method.
• the rules of Cribbage - a popular English pub card game, described by some as ‘a game of low, animal cunning’ .  See https://bicyclecards.com/how-to-play/cribbage
1.1    Your Tasks - Overview
Executive summary [20 marks for each task]:
1. tests/unit .rs: Write some basic unit tests for Card and measure the mutation coverage of the given unit tests and your added unit tests.
2. tests/mbt .rs: Use MBT to write some unit tests for CardDeck.
3. tests/pbt .rs: Use PBT to write tests for cribbage score and peg points.
4. tests/concolic.rs: Use concolic testing to achieve 100% branch coverage of Card::new.
5. report.pdf: Write a reflective report on the benefits and limitations of each test-generation approach.
You will use mutation testing to measure the coverage of your tests after each task.We will perform. the MBT and concolic test generation manually, so that you fully understand how these test generation techniques work.  (There are tools available, but our primary goal here is understanding the test generation approach, so it is better not to use tools at first.)For the property-based testing, we will use the proptest library, which integrates well with Rust and cargo  test.  It is the excellent tool support that makes property-based testing so much fun to use!Your submission for this assignment will include all of your code and output files, plus a report that summarises your results for each section.  This report should be called ‘report.pdf’ and should be placed inside the top-level folder of your submission.  It should start with a title, your full name and student number, then should have one section for each of the following sections of this assignment.  Each section should be as short as possible, ideally just presenting your results as a table and any relevant code fragments or output listings.
1.2    School Policy on Student Misconduct
This assignment is to be completed individually. You are required to read and understand the School Statement on Misconduct, available on the School website at:
https://eecs.uq.edu.au/current-students/guidelines-and-policies-students/student-conduct
1.3   Setup
Download the Cribbage game from Blackboard, unzip it, and run the main program (from inside the unzipped directory) to check that everything works:
cargo  run
You should see output of a simulated Cribbage game, with either Alice or Bob winning.
Then open the project in your favourite Rust IDE. I suggest using Visual Studio Code with the rust-analyzer extension installed, but you can use any IDE that supports Rust.
Now run all tests using cargo  test.  As given to you, there are just some minimal tests in tests/unit .rs.
These should all pass. But are they testing the code thoroughly?
The first part of your assignment will be to measure how thoroughly these tests are testing your SUT...
Note that all through this assignment, you should not change any of the SUT code in the src folder. You should only add tests and output files in the tests folder.
2    Basic  Unit Testing [20 marks]
Your Task: measure the mutation coverage of the given tests (rather insufficient), then write some basic unit tests for Card to improve the coverage of its methods.Use the Rust mutants tool to measure the mutation coverage of all your tests.  Some of your final marks will be based on the mutation coverage of all your tests, to measure the thoroughness of your testing.
Hints:
• Install mutation testing:  cargo  install  --locked  cargo-mutants
• Run mutation testing on lib .rs only:  cargo  mutants  -f  src/lib .rs
• Mutation Testing Docs: https://mutants .rsAdd your results into your report, in a table with at least the following columns (you can add extra columns if you desire).  The example values in square brackets are what you should get when you measure the mutation coverage of just the given tests:
• Strategy: Unit / MBT / PBT / Concolic
• Mut.  Generated: the number of mutations of lib .rs generated [108];
• Mut. Missed: the number of mutations missed [95];
• Mut. Killed: the number of mutations caught (killed) [6];
• Mut. Unviable: mutations that are not viable [7];
• Mut. Percentage: mutation coverage as a percentage [6/108 = 5.5%]; For example:
Strategy
Mut.Gen
Missed
Killed
Unviable
Mut. Coverage%
Given
108
95
6
7
5.5%Note that for the ‘Strategy’ column, ‘Given’ refers to the tests already included with the SUT distribution, and the other options are the various test generation strategies you will use during this assignment. The ‘Mut.Gen’ and ‘Unviable’ columns will be constant over the rows of your table since they depend only upon the SUT file (src/lib .rs in this case), but they are included for completeness.
As well as command line output, full mutation testing output reports are generated in mu- tants.out folder. There you can see which mutants and lines of code are killed or not killed.
Now:
• Write some additional unit tests for the Card class to improve the mutation cover- age of its methods.  Note that you should not change the SUT code, only the tests in tests/unit .rs.
•  Run the mutation coverage tool again to see if you have improved the coverage.
• Record the results in your report, using ‘Unit’ as the strategy.
•  Create a folder tests/unit and copy your mutation results from the mutants .out folder into that new folder to save your mutation coverage results for submission. You can copy just the text files:
mkdir  tests/unit
cp mutants .out/* .txt  tests/unit
ls  -l  tests/unit   #  check  files  were  copied:  caught . txt,  missed . txt,  etc .
3    Model-Based Testing  [20 marks]
Your Task: use model-based testing to test the CardDeck methods.Write a small finite-state machine  (FSM) model of your SUT  (the  CardDeck structure and methods), with about 3-6 states. Since MBT is a black-box technique, do not look at the SUT code, just write your model based on the documentation of the SUT methods and the expected behaviour of the SUT.Manually generate tests from your model, using an all-transitions generation algorithm such as the Chinese postman algorithm. Write the resulting test sequences as Rust unit tests in a new test file called:
tests/mbt .rs
Run your model-based tests to see if the SUT satisfies the properties.Then measure your mutation coverage again and record your results. Make sure you are measuring just the mutation coverage of your MBT tests, not your other unit or PBT or concolic tests.  One simple way of doing this is to just rename those other test files temporarily to tests/* .RUST to prevent them from being run by cargo mutants.
cargo mutants  -f  src/lib .rsAfter you are happy with your MBT tests, record your model in your report (a photo of the hand-written FSM is fine, or you can draw it using software if you prefer).  Add the resulting mutation coverage into your report, usin代 写CSSE4630 Assignment 2: Dynamic Analysis: MBT, PBT, Concolic testing for RustJava
代做程序编程语言g the same table format as in the previous section, but with ‘MBT’ as the strategy. Make sure you are measuring the mutation coverage of only your MBT tests, not your other unit or PBT or concolic tests.
Create the folder tests/mbt and copy your mutation coverage results from the mutants .out folder into that new folder to save them. Just copy the text files.
4    Property-Based Testing  [20 marks]
Your Task: use property-based testing  (and the proptest tool) to test the cribbage   score and peg points functions.
Install the ‘proptest’ library, which is a property-based testing library for Rust.  Then write some properties for the cribbage score and peg points functions, in the file:
tests/pbt .rsTry to make each property test capture some of the interesting behaviour of the SUT. Since PBT is a black-box technique, do not look at the SUT code, just write your properties based on the documentation of the SUT structures and methods.
Run your PBT tests to see if the SUT satisfies the properties.After you have got your properties working with no errors, record your properties in your report with a human-readable explanation / rationale for each property together with the name of that property test method.Then measure the statement and mutation coverage of the SUT using all your PBT tests, and add the resulting statement and mutation coverage into your report as a new row of your coverage table, with strategy ‘PBT’ . Make sure you are measuring the mutation coverage of only your PBT tests, not your other unit or MBT or concolic tests.
Create the folder tests/pbt and copy your mutation coverage results from the mutants .out folder into that new folder to save them. Just copy the text files.
Hints:
• Install proptest:  cargo  add  proptest
• Run as usual: cargo  test
• Docs: https://crates .io/crates/proptest
• Book: https://altsysrq.github .io/rustdoc/proptest/latest/proptest
5    Concolic Testing  [20 marks]Your Task: Use concolic testing (a white-box test technique) to generate tests for the Card::new method, which contains a few simple code paths. Start by calling Card::new(5,  Suit::Hearts). Then use the concolic testing technique to explore all the different code paths through the   method.
Write your generated tests as unit tests, and record them in a new test file: tests/concolic .rsSince concolic testing is a white-box testing technique, you must look at the SUT code as you generate your tests. Your goal is to use concolic testing to achieve 100% condition  coverage of this method (this is a little more demanding than just branch coverage).Each concolic test you generate should contain one main call of your target method. However, before calling your target method you may need to call some constructors and other methods to get the SUT into the required state, and after calling your target method, you may need to call some other query methods so that your JUnit test can check that the test has made the correct state changes to your SUT object or has returned the correct result.
Record each test that you generate in your report as follows:
Run: the number of your test, starting from 1.
Setup:  any constructor calls and setup code needed before calling your
target method;
Target: the  call to your target  SUT method, with input values named
with zero subscripts. For example: foo(x0 = 4, size0  = −10)
Path: this consists of a numbered sequence of assignments and branchconditions showing the path that your test took through the code, including the true/false direction of each branch, and any new or updated variables showing their new values.  Give each updated variable a fresh name by incrementing a counter subscript. (or suffix) on the variable, so that your SMT Constraints can refer to an unambiguous version of the variable. For example:
1. Assign: x1 = x0 + 1
2.  Branch: x1  < 10 −→ true
3. Assign: x2 = x1 * y0
4.  Branch: y0  > 0 −→ false
5.  Return: x2
Output: the output value that your target method returned on this run
(or the exception that it threw);
Next: the number and condition of the branch that you decide to explore
next and the desired direction (true or false);
SMT Constraints: the collected constraints that will enable that direction to betaken- these are what you would send to an SMT solver to calculate a solution, but for this exercise you can calculate a solution using your human solving abilities. Example: x1  = x0+1, not(x1  < 10).
SMT Solution: the output of your (human) ‘SMT solver’ . That is, a value for eachinput variable that satisfies all those constraints. These values will become the input parameters for your next call to your target SUT method. Example: x0 = 9, size0  = 1.
Just as an example, here is how you might record your first concolic-generated test in your report if you were testing a Java ArrayList add method:
Run:  test1
Setup:        s  =  new  ArrayList();   //  sets  ’size_0’  to  0,  ’capacity_0’  to  4 Target:      s.add(elem_0  =  42);
Path:
1:  Branch:  if  (size_0  +  1  < capacity_0) ->  true 2:  Assign:  data_0[size_0]  =  elem_0
3:  Assign:  size_1  =  size_0  +  1 Output:      success  (true)
Next:          if  (size_0  +  1  < capacity_0) ->  false
SMT  Constraints:  capacity_0  =  4,  not(size_0  +  1  < capacity_0)
SMT  Solution:       size_0  ==  3,  so  call  add(_)  3  times,  then  call  add(42) .
Run:  test2
Target:      s.add(elem_0  =  42); Path:
1:  Branch:  if  (size_0  +  1  < capacity_0) ->  false 2:  etc .Also, write each one of these tests as a new test method with the same name (test1, test2 etc.) in your tests/concolic.rs file, so that the connection between your concolic design process and your final test code is clear.When you are satisfied that you have generated enough tests for your target SUT method, measure the coverage metrics of your generated concolic tests on the SUT, and record them in your report as another row in your coverage table, with strategy ‘Concolic’ .
Create the folder tests/concolic and copy your mutation coverage results from the mutants .out folder into that new folder to save them. Just copy the text files.
6    Conclusions and  Reflections [20 marks]Now run ALL your tests (MBT, PBT and Concolic) and calculate their total coverage of the SUT. Record this coverage as the final row in your coverage table, labelled with strategy To- tal.
Create the folder tests/total and copy your mutation coverage results from the mutants .out folder into that new folder to save them. Just copy the text files.
Write a final section in your report entitled ‘Conclusions and Reflections’ (less than one page), that includes:
1. [6 marks] Your coverage table and/or graph summarising your coverage results from each section (Sections 2-5), including coverage results of running ALL the tests on the SUT (the ‘Total’ row).
2. [8 marks] Your Conclusions about the benefits and limitations of each test-generation approach and how they compare;
3. [6 marks] Your Reflections on this assignment and what you learned.
7    Submission
This assignment is due on Friday of Week 13 (25 October 2024, 4pm Queensland time).
Submit your whole repository on Gradescope, in a single *.zip file.  Make sure you include at least:
• your report.pdf file inside the top level folder of your submission;
• the ‘src’ folder with the unchanged SUT code;
• the ‘tests’ folders with all your testing source code, plus the corresponding subfolders with your * .txt mutation coverage output files;
• the ‘proptest-regressions’ folders, which contain results of your proptest runs;
• your Cargo .toml file so that we can build and run your code if necessary.





         
加QQ：99515681  WX：codinghelp  Email: 99515681@qq.com
