+++
authors = ["Mohamed Kamal"]
title = "Understanding XSS Attacks and Contextual Encoding in Go"
date = "2023-12-03"
description = ""
tags = [
    "security",
    "go",
    "xss attacks",
    "http/template",
    "contextual encoding",
]
categories = [
    "security",
    "go",
]
series = ["security"]
+++


## XSS (Cross-Site Scripting)

XSS is a critical security vulnerability that arises when a web application allows an attacker to inject malicious scripts into web pages, which are then viewed by other users. Contextual encoding serves as a crucial defense mechanism against XSS attacks. When user input is presented on a web page, it must undergo proper encoding to ensure that any potential script content is treated as data rather than executable code. This proactive measure helps thwart XSS attacks by guaranteeing that even if malicious code is injected, it will be displayed as plain text rather than executed by the browser.

## Contextual Encoding

The term "context" refers to the specific environment or situation in which a piece of code is executed or data is utilized. It involves understanding the circumstances surrounding a particular occurrence. In web development, particularly when handling user input, context is vital to determining where and how that input will be utilized—whether within an HTML attribute, a JavaScript function, as part of a SQL query, and so on.

Encoding is the process of transforming data from one form to another. In the context of web security, encoding typically involves converting special characters into a format safe for inclusion in a specific context. For example, encoding may replace certain characters with their HTML equivalents. For instance, converting `<` to `&lt;` and `>` to `&gt;` ensures that the browser interprets these characters as plain text and not as HTML tags.

### Example

Consider a scenario where a user submits a review and enters their name as `<script>alert('XSS attack!')</script>`. Without contextual encoding, any user attempting to view a page that displays this username would inadvertently trigger the execution of the embedded JavaScript code. However, with contextual encoding, this input would be encoded to `&lt;script&gt;alert(&#39;XSS attack!&#39;)&lt;/script&gt;`. Notably, the malicious username still appears as intended, but internally it is converted to regular text. The Go template package, by default, incorporates this contextual encoding, thereby safeguarding against potential XSS vulnerabilities. This approach allows the user's input to be displayed as intended, while ensuring that it is internally treated as plain text rather than executable code.

## Trusting Users with `template.HTML`

In situations where complete trust is established with the user, and the content is known to be safe, Go provides the `template.HTML` type. This type explicitly signals to the template system that the content should be treated as raw HTML. While this can be a useful tool, it should be used with caution, as it bypasses the contextual encoding protections and relies on the assumption that the content is entirely trustworthy. Careful consideration should be given to the potential risks before employing this


## Full Go Example

The provided Go code illustrates a simple web application using the Echo framework that allows users to submit reviews through a form. The example showcases how contextual encoding is applied by default through the Go template package to mitigate potential XSS vulnerabilities.


**main.go**
```go
package main

import (
	"html/template"
	"io"
	"net/http"
	"strconv"

	"github.com/labstack/echo/v4"
)

type Template struct {
	templates *template.Template
}

func (t *Template) Render(w io.Writer, name string, data interface{}, c echo.Context) error {
	return t.templates.ExecuteTemplate(w, name, data)
}

type ReviewData struct {
	Username string
	Rating   int
	Comment  string
}

func main() {
	e := echo.New()

	e.GET("/review", GetReviewForm)
	e.POST("/review", SubmitReview)

	t := &Template{
		templates: template.Must(template.ParseGlob("public/views/*.html")),
	}
	e.Renderer = t

	e.Logger.Fatal(e.Start(":1323"))
}

func GetReviewForm(c echo.Context) error {
	return c.Render(http.StatusOK, "review_form.html", nil)
}

func SubmitReview(c echo.Context) error {
	// Retrieve form values
	username := c.FormValue("username")
	ratingStr := c.FormValue("rating")
	comment := c.FormValue("comment")

	// Convert rating to int
	rating, err := strconv.Atoi(ratingStr)
	if err != nil {
		return c.String(http.StatusBadRequest, "Invalid rating")
	}

	// Create a struct to hold the review data
	reviewData := ReviewData{
		Username: username,
		Rating:   rating,
		Comment:  comment,
	}

	// Render a confirmation page with the submitted review data
	return c.Render(http.StatusOK, "review_confirmation.html", reviewData)
}
```
**review_form.html**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Review Form</title>
</head>
<body>
    <h1>Review Form</h1>
    
    <form action="/review" method="post">
        <label for="username">Username:</label>
        <input type="text" id="username" name="username" required>
        
        <label for="rating">Rating:</label>
        <input type="number" id="rating" name="rating" min="1" max="5" required>
        
        <label for="comment">Comment:</label>
        <textarea id="comment" name="comment" rows="4" required></textarea>
        
        <button type="submit">Submit Review</button>
    </form>
</body>
</html>

```

**review_confirmation.html**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Review Confirmation</title>
</head>
<body>
    <h1>Review Submitted</h1>
    <p>Thank you for your review!</p>

    <h2>Review Details:</h2>
    <p>Username: {{.Username}}</p>
    <p>Rating: {{.Rating}}</p>
    <p>Comment: {{.Comment}}</p>
</body>
</html>
```
### Try It Yourself: Contextual Encoding in Action

1. **Go and View the Page Source**

   After running the provided Go example, navigate to the review form at `http://localhost:1323/review` in your web browser. Submit a review with the name `<script>alert('XSS attack!')</script>`. Once submitted, right-click on the page and select "View Page Source" to inspect the HTML. Observe how the entered script is encoded as `&lt;script&gt;alert(&#39;XSS attack!&#39;)&lt;/script&gt;`, preventing it from being executed.

---

### Try It Yourself: Using `template.HTML`

2. **Using `template.HTML` to Trigger Alert Script**

   To observe the potential risk when using `template.HTML`, modify the `ReviewData` struct in `main.go` to use `template.HTML` for the comment field:

   ```go
   type ReviewData struct {
       Username string
       Rating   int
       Comment  template.HTML
   }
   ```

   Then, in the `SubmitReview` function, construct the `ReviewData` using `template.HTML` for the comment:

   ```go
   // Create a struct to hold the review data
   reviewData := ReviewData{
       Username: username,
       Rating:   rating,
       Comment:  template.HTML(comment),
   }
   ```

   Submit a new review with the name `<script>alert('XSS attack!')</script>` again. After confirming the review, view the confirmation page. You'll notice that the script is now triggered automatically, indicating the potential risk of using `template.HTML` without proper validation.

---

### Try It Yourself: Avoiding the Template Package Entirely

3. **Avoiding the Template Package Entirely**

   In this scenario, modify the `SubmitReview` function to directly write to the response writer using `Fprint`:

   ```go
   func SubmitReview(c echo.Context) error {
       // Retrieve form values
       username := c.FormValue("username")
       ratingStr := c.FormValue("rating")
       comment := c.FormValue("comment")

       // Convert rating to int
       rating, err := strconv.Atoi(ratingStr)
       if err != nil {
           return c.String(http.StatusBadRequest, "Invalid rating")
       }

       // Construct the HTML for the confirmation page
       confirmationHTML := fmt.Sprintf(`
           <!DOCTYPE html>
           <html lang="en">
           <head>
               <meta charset="UTF-8">
               <meta name="viewport" content="width=device-width, initial-scale=1.0">
               <title>Review Confirmation</title>
           </head>
           <body>
               <h1>Review Submitted</h1>
               <p>Thank you for your review!</p>

               <h2>Review Details:</h2>
               <p>Username: %s</p>
               <p>Rating: %d</p>
               <p>Comment: %s</p>
           </body>
           </html>
       `, username, rating, comment)

       // Write the HTML directly to the response writer
       io.WriteString(c.Response().Writer, confirmationHTML)

       return nil
   }
   ```
   ---