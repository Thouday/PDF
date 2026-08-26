# Broken Links Testing Using Selenium Java

## What is a Broken Link?

A broken link is a hyperlink that does not lead to a valid resource.

Common HTTP status codes:

- 200 - OK
- 301 - Permanent Redirect
- 302 - Temporary Redirect
- 400 - Bad Request
- 401 - Unauthorized
- 403 - Forbidden
- 404 - Not Found
- 500 - Internal Server Error

Generally, status codes >= 400 indicate that the link may be broken.

---

## How to Find Broken Links Using Selenium

Selenium is used to:

1. Open the webpage.
2. Find all `<a>` elements.
3. Get the `href` attribute.
4. Send an HTTP request to each URL.
5. Check the HTTP response status code.
6. Report links with status codes >= 400.

---

## Example

```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.util.List;

public class BrokenLinks {

    public static void main(String[] args) {

        WebDriver driver = new ChromeDriver();

        driver.get("https://example.com");

        List<WebElement> links = driver.findElements(By.tagName("a"));

        HttpClient client = HttpClient.newHttpClient();

        for (WebElement link : links) {

            String href = link.getAttribute("href");

            if (href == null || href.isEmpty()) {
                continue;
            }

            try {

                HttpRequest request = HttpRequest.newBuilder()
                        .uri(URI.create(href))
                        .GET()
                        .build();

                HttpResponse<Void> response =
                        client.send(
                                request,
                                HttpResponse.BodyHandlers.discarding()
                        );

                int statusCode = response.statusCode();

                if (statusCode >= 400) {
                    System.out.println(
                            "Broken Link: " + href +
                            " | Status Code: " + statusCode
                    );
                } else {
                    System.out.println(
                            "Valid Link: " + href +
                            " | Status Code: " + statusCode
                    );
                }

            } catch (Exception e) {
                System.out.println(
                        "Unable to access: " + href
                );
            }
        }

        driver.quit();
    }
}
