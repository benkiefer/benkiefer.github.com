---
layout: post
title: "PowerMockito: Constructor Mocking"
date: 2013-04-23 18:17
comments: false
categories: 
 - mocking
 - Mockito
 - PowerMock
 - Java
 - JUnit
---

Mockito provides mechanisms for mocking most of the stuff you need, but remains mysteriously silent on constructor mocking. "Newing up" an object when you are mocking in your unit tests is a pain, and I often found myself writing tiny factories to isolate where I was using a constructor.

For example, to test the following code...

```
public class Helper {
 
	public void doSomething(String arg){
		new Something(arg).doSomething();
	}

}
```
	 
<!-- more -->
	 
You might end up writing something like this to make it more testable...
 
```
public class Helper {
	  
	private SomethingFactory somethingFactory;
		  
	public void doSomething(String arg) {
		Something something = somethingFactory().build(arg);
		something.doit();
	}
	
	public void setSomethingFactory(SomethingFactory somethingFactory) {
		this.somethingFactory = somethingFactory;
	}
	 
}
```
	 
Now you can inject a mock SomethingFactory in your test, and all the real SomethingFactory does is create your object with the appropriate arg and return it.
	 
PowerMockito offers an enhancement to the Mockito API that let's you sidestep this problem with its "whenNew" method, but their documentation is lacking.

To use, "whenNew", you will need to supplement your typical Mockito test with a few enhancements.

```
@RunWith(PowerMockRunner.class)
@PrepareForTest(Helper.class)
public class HelperTest {
	@Mock
	private Something mockSomething;
		
	@InjectMocks
	private Helper helper;
		
	@Test
	public void doSomething() throws Exception {
	 	String argument = "arg";
			
		PowerMockito.whenNew(Something.class).withArguments(argument).thenReturn(mockSomething);
			
		helper.doSomething(argument);
			
		verify(mockSomething).doIt();
	}
	
}
```

The important changes over a typical Mockito test are:

 * The use of the PowerMockRunner in our JUnit @RunWith annotation
 * The use of the @PrepareForTest method where we specify the name of the class we are testing (Helper.class in this case).
 * The "throws Exception" on our method signature caused by the usage of "whenNew"
 * The static invocation of the "whenNew" method, and our passing in the "argument" parameter".

With these changes, you can roll back to the previous code, getting rid of your unnecessary factory and getting rid of some of the clutter from our Helper java class.