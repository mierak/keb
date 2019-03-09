# Keb
## Page object pattern
Keb is a Selenium wrapper written in Kotlin that allows you to modularize pages of your web application into logic units represented by Kotlin classes. Design of Keb is heavily inspired by https://gebish.org.
```kotlin
class KotlinHomePage(browser: Browser) : Page(browser) {
    override fun url() = "https://kotlinlang.org"
}
```
Please note that you must define class with browser object in constructor and pass this value into Page superclass.

### Page elements
To select web element on your page use following methods.
```kotlin
css(".my-selector") // returns single DOM element by CSS selector
ccsList(".my-select") // returns list of all elements found by CSS selector
// following selectors are also available
html("h1")
htmlList("h1")
xpath("/html/body/h1")
xpathList("/html/body/h1")
```

### Page verifier
In order to verify, that you successfully landed on your page, you can use at() method.
```kotlin
class KotlinHomePage(browser: Browser) : Page(browser) {
    override fun url() = "https://kotlinlang.org"
    
    val header = css(".global-header-logo")
    override fun at() = header
}
```

### Modules
If you want to reuse page content present on multiple pages, you can use modules. There are two types of modules in keb:
* Module - same functionality as page, but can be reused on multiple places
* ScopedModule - same as Module, but content is searched only in scope passed as constructor argument.
```kotlin
class NavMenuModule(browser: Browser, scope: WebElement) : ScopedModule(browser, scope) {
    val menuItems = htmlList("a")
}

class KotlinHomePage(browser: Browser) : Page(browser) {
    override fun url() = "https://kotlinlang.org"
    
    val menu = scopedModule(::NavMenuModule, css(".nav-links"))
}
```

## Waiting
Waiting for some page element to be present, you can use waitFor method.
```kotlin
css(".form-input").click()
val result = waitFor {
    css(".result")
}
``` 

## Usage
```kotlin
// configuration
val config = kebConfig {
    driver = FirefoxDriver()
}
Browser.drive(config) {
    val kotlinHomePage = to(::KotlinHomePage)
    kotlinHomePage.menu.first().click()
    // ...
}
```
For full usage example and how to use keb with JUnit please refer to [/src/test/kotlin/com/horcacorp/testing/keb](/src/test/kotlin/com/horcacorp/testing/keb).

## TODOs
* load configuration from external file
* Maven repo