# Playwright Web Testing Framework

A reusable end-to-end test automation framework built with **Python + Playwright + pytest**, using the Page Object Model (POM) design pattern.

## 📁 Project Structure

```
playwright-web-testing-framework/
├── pages/
│   ├── base_page.py
│   ├── login_page.py
│   └── dashboard_page.py
├── tests/
│   ├── test_login.py
│   ├── test_navigation.py
│   └── test_form_submission.py
├── utils/
│   └── helpers.py
├── conftest.py
├── pytest.ini
└── requirements.txt
```

## 🛠️ Tech Stack

- Python 3.10+
- Playwright
- pytest
- pytest-playwright

## ⚙️ Setup

```bash
# Clone the repo
git clone https://github.com/ashwini-qa/playwright-web-testing-framework.git
cd playwright-web-testing-framework

# Install dependencies
pip install -r requirements.txt

# Install browsers
playwright install
```

## ▶️ Run Tests

```bash
# Run all tests
pytest

# Run with headed browser (visible)
pytest --headed

# Run specific test file
pytest tests/test_login.py

# Run with HTML report
pytest --html=reports/report.html
```

---

## 📄 Sample Code

### `pages/base_page.py`

```python
class BasePage:
    def __init__(self, page):
        self.page = page

    def navigate(self, url):
        self.page.goto(url)

    def get_title(self):
        return self.page.title()

    def wait_for_element(self, selector, timeout=5000):
        self.page.wait_for_selector(selector, timeout=timeout)

    def take_screenshot(self, name):
        self.page.screenshot(path=f"screenshots/{name}.png")
```

---

### `pages/login_page.py`

```python
from pages.base_page import BasePage

class LoginPage(BasePage):
    URL = "https://the-internet.herokuapp.com/login"

    USERNAME_INPUT = "#username"
    PASSWORD_INPUT = "#password"
    LOGIN_BUTTON   = "button[type='submit']"
    SUCCESS_MSG    = ".flash.success"
    ERROR_MSG      = ".flash.error"

    def navigate_to_login(self):
        self.navigate(self.URL)

    def login(self, username, password):
        self.page.fill(self.USERNAME_INPUT, username)
        self.page.fill(self.PASSWORD_INPUT, password)
        self.page.click(self.LOGIN_BUTTON)

    def get_success_message(self):
        return self.page.text_content(self.SUCCESS_MSG)

    def get_error_message(self):
        return self.page.text_content(self.ERROR_MSG)
```

---

### `tests/test_login.py`

```python
import pytest
from pages.login_page import LoginPage

class TestLogin:

    def test_valid_login(self, page):
        login = LoginPage(page)
        login.navigate_to_login()
        login.login("tomsmith", "SuperSecretPassword!")
        assert "You logged into a secure area!" in login.get_success_message()

    def test_invalid_password(self, page):
        login = LoginPage(page)
        login.navigate_to_login()
        login.login("tomsmith", "wrongpassword")
        assert "Your password is invalid!" in login.get_error_message()

    def test_empty_credentials(self, page):
        login = LoginPage(page)
        login.navigate_to_login()
        login.login("", "")
        assert "Your username is invalid!" in login.get_error_message()
```

---

### `tests/test_navigation.py`

```python
import pytest
from pages.base_page import BasePage

class TestNavigation:

    def test_page_title(self, page):
        base = BasePage(page)
        base.navigate("https://the-internet.herokuapp.com")
        assert "The Internet" in base.get_title()

    def test_page_loads_successfully(self, page):
        base = BasePage(page)
        base.navigate("https://the-internet.herokuapp.com")
        base.wait_for_element("h1")
        heading = page.text_content("h1")
        assert heading == "Welcome to the-internet"
```

---

### `conftest.py`

```python
import pytest
from playwright.sync_api import sync_playwright

@pytest.fixture(scope="function")
def page():
    with sync_playwright() as p:
        browser = p.chromium.launch(headless=True)
        context = browser.new_context(
            viewport={"width": 1280, "height": 720}
        )
        page = context.new_page()
        yield page
        context.close()
        browser.close()
```

---

### `requirements.txt`

```
playwright==1.43.0
pytest==8.1.1
pytest-playwright==0.5.0
pytest-html==4.1.1
```

---

## ✅ Test Coverage

| Test Area | Status |
|---|---|
| Login — valid credentials | ✅ |
| Login — invalid credentials | ✅ |
| Login — empty fields | ✅ |
| Page navigation | ✅ |
| Page title validation | ✅ |
| Form submission | ✅ |
| Cross-browser (Chromium, Firefox, WebKit) | ✅ |

---

## 👩‍💻 Author

**Ashwini Deshpande** — ISTQB Certified QA Engineer
[LinkedIn](https://www.linkedin.com/in/dashwini) 
