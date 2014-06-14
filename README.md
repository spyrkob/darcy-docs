Automating Applications with Darcy
=========

Darcy is a framework for writing [**page objects**](http://martinfowler.com/bliki/PageObject.html) in order to automate interaction with graphical user interfaces. Page objects are classes that model what a user can see and do with a specific page. In darcy each page, or subset of a page, is called a [View](https://github.com/darcy-framework/darcy/blob/master/src/main/java/com/redhat/darcy/ui/View.java).

**darcy** is:

* Automation library agnostic -- any library that can find UI elements and interact with them can work with darcy. [Selenium WebDriver](https://code.google.com/p/selenium/) support is provided by [darcy-webdriver](https://github.com/darcy-framework/darcy-webdriver).
* Flexible and extendable by virtue of a declarative, **element-based DSL**. Write your page objects in terms of the UI buttons, labels, and widgets that you see. Abstract complicated interactions in reusable, custom element types with high-level APIs.
* Open source and licensed under [version 3 of the GPL](https://www.gnu.org/copyleft/gpl.html).
* Dependent on Java 8. [Get your lambda on](http://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html)!

Example page object
===================
```java
@RequireAll
public class MyHomePage extends AbstractView {
  private TextInput login = textInput(By.id("login"));
  private TextInput password = textInput(By.id("password"));
  private Button submit = button(By.id("submit"));

  @NotRequired
  private Label errorMsg = label(By.class("error"));

  public AccountDetails login(Credentials credentials) {
    login.clearAndType(credentials.login());
    password.clearAndType(credentials.password());

    return after(submit::click)
        .expect(transition().to(new AccountDetails())
        .failIf(errorMsg::isDisplayed)
          .throwing(new LoginException(credentials, errorMsg.readText()))
        .waitUpTo(1, MINUTES);
  }
}
```