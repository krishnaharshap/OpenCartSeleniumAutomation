# OpenCart Selenium Automation

A modular Selenium + Java + TestNG framework testing the [OpenCart demo storefront](https://naveenautomationlabs.com/opencart/index.php?route=account/login) end to end: login, home page search, product detail, and registration.

## Tech Stack

- Java 17, Maven
- Selenium WebDriver 4.39
- TestNG 7.7 (Page Object Model, data providers, soft assertions)
- Allure TestNG + ChainTest for reporting
- OpenCSV (available for data-driven tests, not currently wired into any suite)

## Test Coverage

| Test class | Covers |
|---|---|
| `LoginPageTest` | Page title/URL, forgot-password link presence, successful login |
| `HomePageTest` | Post-login title, logout link, header nav items, product search (data-driven) |
| `ProductInfoPageTest` | Product detail header, image counts, full product info fields (soft assertions) |
| `RegisterPageTest` | New user registration (data-driven) |

Since this targets a public third-party demo site, occasional test failures can come from real
site hiccups (slow load, transient timeouts) rather than framework or app regressions -- rerun
before assuming a genuine break.

## Project Structure

```text
src/main/java/com/opencart/qa/
  exceptions/   AppError, BrowserException, ElementException, FrameworkException
  factory/      DriverFactory (thread-local WebDriver, chrome/firefox/edge/safari), OptionsManager
  listeners/    TestAllureListener
  pages/        HomePage, LoginPage, ProductInfoPage, RegisterPage, ResultsPage
  utils/        AppConstants, ElementUtil, JavaScriptUtil, StringUtil, CSVUtil, ExcelUtil

src/test/java/com/opencart/qa/
  base/         BaseTest -- driver setup/teardown, page object wiring
  tests/        LoginPageTest, HomePageTest, ProductInfoPageTest, RegisterPageTest

src/test/resources/
  config/config.properties           browser, target URL, test login credentials
  testrunners/testng_sanity.xml      Login Page Test only -- fast feedback
  testrunners/testng_regression.xml  all 4 test classes -- full suite (Maven default)
  chaintest.properties               ChainTest HTML/email report generator config
```

## Run Locally

Install dependencies and run the default suite (regression, all 4 test classes):

```bash
mvn clean test
```

Run just the fast sanity suite (Login Page Test only):

```bash
mvn clean test -DsuiteXmlFile=src/test/resources/testrunners/testng_sanity.xml
```

Override the browser (`config.properties` defaults to `chrome`):

```bash
mvn clean test -Dbrowser=firefox
```

Test login credentials in `config.properties` are for the public OpenCart demo instance, not a
real account.

## Reports

- Allure results: `allure-results/` (`allure serve allure-results` to view, requires the Allure CLI)
- ChainTest HTML: `target/chaintest/Index.html`
- Failure screenshots are attached to the ChainTest report automatically via `BaseTest`

## CI

`.github/workflows/ci.yml` runs the full regression suite on every push and PR to `main`, headless
Chrome on `ubuntu-latest`, and uploads the ChainTest and Allure output as build artifacts.
