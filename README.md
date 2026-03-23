# README

This project contains a Selenium-based Java class (`WebDriver`) that opens a browser, logs timestamps, optionally starts a packet capture, and runs a set of website-specific methods on a schedule.

Use this only with accounts, systems, and traffic captures you are authorized to access.


## Eclipse and Selenium setup

Set up the Java project so Selenium can compile and launch Chrome correctly.

### 1. Install Java
### 2. Download Eclipse
Install Eclipse IDE for Java Developers
### 3. Download Selenium Java
Download the **Selenium Java client** package. After extracting it, you will typically see:
- the main Selenium `.jar` files
- a `libs/` folder containing additional dependency jars
You need **both** the top-level Selenium jars and **all jars inside `libs/`** on the project's build path.
### 4. Add Selenium jars in Eclipse
In Eclipse:
1. Create or open your Java project.
2. Right-click the project.
3. Choose **Build Path -> Configure Build Path**.
4. Open the **Libraries** tab.
5. Click **Add External JARs...** and select the Selenium jar files.
6. Click **Add External JARs...** again and also select every jar inside Selenium's `libs` folder.
7. Apply and close.

### 5. Set up Chrome and ChromeDriver

This project uses `ChromeDriver`, so you need:

- Google Chrome installed
- a compatible ChromeDriver available to the project
### 6. Confirm the project runs before scheduling anything

Before using the full site list, verify that:
- Eclipse shows no unresolved Selenium imports
- Chrome opens successfully from a minimal test
- your JDK, Chrome version, and ChromeDriver version are compatible


## Selnium Script: What needs to be edited before running


The pasted source includes several placeholder strings such as:
- `"Enter ID"`
- `"Enter Email"`
- `"Enter Password"`
- `"Enter List of Emails"`
- `"Enter Time"`
These are intentional placeholders and must be replaced with your own configuration values before the code can run correctly.

## Where placeholders appear

Common places you will need to edit:
- Website login methods such as `outlook()`, `office()`, `netflix()`, `quora()`, `facebook()`, `instagram()`, `pinterest()`, and `gmail()`
- `runWithFunction(...)` and `stopTcpDump(...)`, where `sudo` currently uses `"Enter Password"`
- The `main(...)` method, where `runTimes` is currently set to `List.of("Enter Time")`

## Recommended way to handle credentials
A safer pattern is:
1. Keep placeholders in the code while developing.
2. Load real values from environment variables or a local config file that is not committed.
3. Use dedicated test accounts where possible.
Example idea:
```java
String email = System.getenv("OUTLOOK_EMAIL");
String password = System.getenv("OUTLOOK_PASSWORD");
```
Then use those variables instead of literal strings in the login methods.

## Editing the website methods
Updating the website methods:
The web elements needs constant update as the element id frequently changes in the website. 
It is helpful to make sure they are all up-to-date before running the script.

Most login methods follow the same pattern:

```java
WebElement id = wait.until(ExpectedConditions.elementToBeClickable(...));
String input = "Enter ID";
for (char c : input.toCharArray()) {
    id.sendKeys(String.valueOf(c));
}
```
To keep the project placeholder-based, replace hardcoded real values with one of these:

- a neutral placeholder like `"Enter ID"`
- a variable loaded from environment/config
Example:

```java
String input = System.getenv("SITE_ID");
```

and later:

```java
input = System.getenv("SITE_PASSWORD");
```
If you want the file to remain shareable without secrets, leave the placeholders in source and document the expected environment variables separately.

## How `main(...)` works
The `main` function does three things:
1. Creates a scheduler:
2. Defines the list of site methods to run:
3. Defines the times when the full batch should run:
## How to set up `main(...)` correctly

### 1. Make sure every function name is valid
Every item in `functions` must match a `case` in this switch block:
If you add a new website method, you must do both:
- create the method, such as `public void exampleSite()`
- add a matching switch case:
```java
case "examplesite":
    driver.exampleSite();
    break;
```
- add `"examplesite"` to the `functions` list if you want it scheduled
### 2. Replace `runTimes`
Change:

```java
List<String> runTimes = List.of("Enter Time");
```
To one or more valid times:

```java
List<String> runTimes = List.of("08:00");
```
or:

```java
List<String> runTimes = List.of("08:00", "20:00");
```

### 3. Check the timezone
The code currently uses:
```java
String timeZoneId = "America/New_York";
```
If your scheduled times should follow a different timezone, change that value.

### 4. Check the output folder naming

Inside the scheduled task, filenames are built using:

```java
String filename = number + "_" + dateString + "_" + "eduroam" + "_";
```

If you want a different network label or folder naming convention, edit that string.

### 5. Verify the packet capture interface

`runWithFunction(...)` currently starts tcpdump with:

```java
"-i", "en0"
```

That interface name is system-specific. Confirm that it matches your machine.

## Minimal safe test setup

Before enabling a large `functions` list, test with one site and one run time:

```java
List<String> functions = List.of("google");
List<String> runTimes = List.of("09:00");
```

This makes it easier to verify when the code needs update due to changes in the html 
## File summary

- `main(...)`: schedules batch runs
- `runWithFunction(...)`: starts capture, opens browser, dispatches a site method, tears down
- `stopTcpDump(...)`: stops the packet capture process
- site methods like `gmail()`, `outlook()`, `facebook()`: browser automation for each site


