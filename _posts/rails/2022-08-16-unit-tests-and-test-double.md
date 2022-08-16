---
layout: post
title: "Unit test and test double"
subtitle: "Ruby on Rails with Rspec"
date: 2022-08-15
author: "Theo Cha"
tags:
    - Ruby on Rails
    - Rspec
    - TDD
---


> Fail first, I am not afraid of failing. I am saying this in my real life. For example:

![Uncle Bob](/img/post/unit-test-test-double-01.png "Uncle Bob")

Before starting to say anything about testing, I can’t forget to say about Uncle bob because I have learned TDD from his book – ‘Clean code’.

The Three Rules of TDD

Robert C. Martin (“Uncle Bob”) provides a concise set of rules for practicing TDD.
-	Write production code only to pass a failing unit test.
-	Write no more of a unit test than sufficient to fail (compilation failures are failures).
-	Write no more production code than necessary to pass the one failing unit test.

With above those three principles, I would say we understand many parts of TDD.


### TDD benefits
----------------------------------------

When I see the question, the first thing that comes to my brain is that writing tests make me comfortable and confident in what I am doing. Especially when a team works together, it makes many things easier. There are still many reasons why we write tests.

1.	Writing the tests first requires you to really consider what you want from the code.
2.	You receive fast feedback.
3.	TDD creates a detailed specification.
4.	TDD reduces time spent on rework.
5.	You spend less time in the debugger.
6.	You are able to identify the errors and problems quickly.
7.	TDD tells you whether your last change (or refactoring) broke previously working code.
8.	TDD allows the design to evolve and adapt to your changing understanding of the problem.
9.	TDD forces the radical simplification of the code. You will only write code in response to the requirements of the tests.
10.	You're forced to write small classes focused on one thing.
11.	TDD creates SOLID code.
12.	…
[20 benefits of TDD](https://dzone.com/articles/20-benefits-of-test-driven-development)

### Unit tests, mocks, fakes, stubs.
----------------------------------------

***Why Unit Test?***
To test the functionality of out code rather than finding bug that may exist in our system.

***Unit Test are***
-	Tests written for testing a unit of code
-	One unit test runs independently
-	External dependencies are managed with doubles(mock/fakes/stubs)
-	Should complete within milliseconds


#### Writing unit tests in Rails
----------------------------------------

***Arrange – Act – Assert (AAA)***

```cpp
def sum(a,b)
  return a + b
end

it '.sum' do
  //Arrange
  first = 100
  second = 200

  //Act
  result = sum(first, second)

  //Assert
  expect(result).to eq(first+second)
end
```

#### Test double

-	We Used in External dependencies where may cause side effect
-	Mocks, Fakes, Stubs

----------------------------------------

#### Stub

![Stub](/img/post/unit-test03.png "Stub")
Illustration | Credit: [Michal Lipski](https://blog.pragmatists.com/test-doubles-fakes-mocks-and-stubs-1a7491dfa3da)

-	Generates predefined outputs
-	Programmed stub to return a success, failure, or exception
-	What to test?
  - Returns success, failure, or exception (as coded)
  - Checks the behavior of code under test in case of these return values


```cpp
public class GradesService {
    private final Gradebook gradebook;

    public GradesService(Gradebook gradebook) {
        this.gradebook = gradebook;
    }

    Double averageGrades(Student student) {
        return average(gradebook.gradesFor(student));
    }
}

public class GradesServiceTest {
    private Student student;
    private Gradebook gradebook;

    @Before
    public void setUp() throws Exception {
        gradebook = mock(Gradebook.class);
        student = new Student();
    }

    @Test
    public void calculates_grades_average_for_student() {
        when(gradebook.gradesFor(student)).thenReturn(grades(8, 6, 10)); //stubbing gradebook
        double averageGrades = new GradesService(gradebook).averageGrades(student);
        assertThat(averageGrades).isEqualTo(8.0);
    }
}
```
Source | [Michal Lipski](https://blog.pragmatists.com/test-doubles-fakes-mocks-and-stubs-1a7491dfa3da)


----------------------------------------
#### Mock

![Mock](/img/post/unit-test02.png "Mock")
Illustration | Credit: [Michal Lipski](https://blog.pragmatists.com/test-doubles-fakes-mocks-and-stubs-1a7491dfa3da)

-	Mocks replaces external interface
-	Mocks are not used for checking function’s behavior or return values from the function call
-	What to test?
  - Mock function called or not?
  - How many times it is called?
  - What params are passed?
  - When was it called?
  - Right call, right number of times with right params.


```cpp
public class SecurityCentral {
    private final Window window;
    private final Door door;

    public SecurityCentral(Window window, Door door) {
        this.window = window;
        this.door = door;
    }

    void securityOn() {
        window.close();
        door.close();
    }
}
```
Source | [Michal Lipski](https://blog.pragmatists.com/test-doubles-fakes-mocks-and-stubs-1a7491dfa3da)

----------------------------------------
#### Fake

![Fake](/img/post/unit-test03.png "Fake")
Illustration | Credit: [Michal Lipski](https://blog.pragmatists.com/test-doubles-fakes-mocks-and-stubs-1a7491dfa3da)

-	Almost working implementation
-	Connect to a local HTTP server
-	What to test?
  - Instead of going to the internet it connects to a local implementation
  - Check the behavior with respect to the actual data it receives from the server.


```cpp
@Profile("transient")
public class FakeAccountRepository implements AccountRepository {

       Map<User, Account> accounts = new HashMap<>();

       public FakeAccountRepository() {
              this.accounts.put(new User("john@bmail.com"), new UserAccount());
              this.accounts.put(new User("boby@bmail.com"), new AdminAccount());
       }

       String getPasswordHash(User user) {
              return accounts.get(user).getPasswordHash();
       }
}
```
Source | [Michal Lipski](https://blog.pragmatists.com/test-doubles-fakes-mocks-and-stubs-1a7491dfa3da)


References
----------

- <https://blog.pragmatists.com/test-doubles-fakes-mocks-and-stubs-1a7491dfa3da>
- <https://www.youtube.com/watch?v=NPp2pvhGbkM>









