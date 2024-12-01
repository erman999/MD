# **PHP Auto Layout (Page Class)**

This documentation introduces the `Page` class, a flexible and secure solution for generating HTML layouts in PHP. It helps create a basic structure for your web pages, including meta tags, CSS styles, and JavaScript scripts. The class is designed to leave the `<body>` tag empty, giving you full control over the content.

---

## **Overview**

The `Page` class simplifies the creation of PHP-based web pages by automating repetitive tasks like including styles, scripts, and meta tags. It is designed with security and scalability in mind, allowing easy customization for different projects.

---

## **Features**

- **Dynamic Meta Tags**: Includes customizable meta tags for title, theme color, and more.
- **CSS and JavaScript Management**: Easily add styles and scripts with optional versioning for cache busting.
- **Error Reporting**: Built-in method for enabling error reporting during development.
- **Security**: Escapes all dynamic content to prevent XSS attacks.
- **Customizable**: Fully extensible to meet the needs of different projects.

---

## **Code Implementation**

Below is the full code for the `Page` class:

```php
<?php

class Page {

    // Default variables for the page
    public $variables = [
        'title' => 'Default Title', // Default page title
        'theme-color' => '#00d1b2', // Default theme color
    ];

    // CSS styles to include
    public $styles = [
        ['file' => '/public/css/bulma.min.css', 'update' => 0],
        ['file' => '/public/css/styles.css', 'update' => 1],
    ];

    // JavaScript files to include
    public $scripts = [
        ['file' => '/public/js/chart.min.js', 'update' => 0],
        ['file' => '/public/js/scripts.js', 'update' => 1],
    ];

    /**
     * Outputs the page's HTML header, including meta tags, styles, and other elements.
     */
    public function header() {
        echo '<!DOCTYPE html>' . PHP_EOL;
        echo '<html lang="tr" dir="ltr">' . PHP_EOL;
        echo '<head>' . PHP_EOL;
        echo '<title>' . htmlspecialchars($this->variables['title'], ENT_QUOTES, 'UTF-8') . '</title>' . PHP_EOL;
        echo '<meta charset="utf-8">' . PHP_EOL;
        echo '<meta name="viewport" content="width=device-width, initial-scale=1">' . PHP_EOL;
        echo '<meta http-equiv="X-UA-Compatible" content="IE=edge">' . PHP_EOL;
        echo '<meta name="theme-color" content="' . htmlspecialchars($this->variables['theme-color'], ENT_QUOTES, 'UTF-8') . '">' . PHP_EOL;
        echo '<link rel="icon" href="/favicon.ico">' . PHP_EOL;
        echo $this->style_looper($this->styles);
        echo '</head>' . PHP_EOL;
        echo '<body>' . PHP_EOL;
    }

    /**
     * Outputs the page's HTML footer, including JavaScript files.
     */
    public function footer() {
        echo $this->script_looper($this->scripts);
        echo '</body>' . PHP_EOL;
        echo '</html>' . PHP_EOL;
    }

    /**
     * Enables error reporting for debugging purposes.
     */
    public function show_errors() {
        error_reporting(E_ALL);
        ini_set('display_errors', 1);
    }

    /**
     * Appends a random version query string to file paths if update flag is set.
     *
     * @param int $update
     * @return string|null
     */
    private function updater($update) {
        return $update === 1 ? '?v=' . rand() : '';
    }

    /**
     * Loops through styles and generates HTML link tags.
     *
     * @param array $array
     * @return string
     */
    private function style_looper(array $array) {
        $output = '';
        foreach ($array as $item) {
            $output .= '<link rel="stylesheet" href="' . htmlspecialchars($item['file'], ENT_QUOTES, 'UTF-8') . $this->updater($item['update']) . '">' . PHP_EOL;
        }
        return $output;
    }

    /**
     * Loops through scripts and generates HTML script tags.
     *
     * @param array $array
     * @return string
     */
    private function script_looper(array $array) {
        $output = '';
        foreach ($array as $item) {
            $output .= '<script src="' . htmlspecialchars($item['file'], ENT_QUOTES, 'UTF-8') . $this->updater($item['update']) . '" charset="utf-8"></script>' . PHP_EOL;
        }
        return $output;
    }

}

?>
```

---

## **Usage**

Below is an example of how to use the `Page` class in a PHP project:

### **Basic Usage**

```php
<?php
require_once 'Page.php';

// Create a new Page instance
$page = new Page();

// Customize page variables
$page->variables['title'] = 'My Custom Page Title';
$page->variables['theme-color'] = '#123456';

// Add additional styles and scripts
$page->styles[] = ['file' => '/public/css/custom.css', 'update' => 1];
$page->scripts[] = ['file' => '/public/js/custom.js', 'update' => 0];

// Render the page
$page->header(); // Outputs the HTML header

// Add your content here
echo '<h1>Welcome to My Page!</h1>';

$page->footer(); // Outputs the HTML footer
?>
```

---

## **Best Practices**

1. **Secure Content**:
   - Always use `htmlspecialchars` to escape dynamic data.
   - Avoid including sensitive data like database credentials in the class.

2. **Custom Styles and Scripts**:
   - Use the `$styles` and `$scripts` arrays to manage your assets dynamically.
   - Enable versioning (`update => 1`) for assets that may change frequently.

3. **Error Handling**:
   - Use `show_errors()` only in a development environment. Disable error reporting in production.

4. **Separation of Concerns**:
   - Keep the `Page` class focused on layout generation.
   - Handle business logic and data processing separately.

---

## **Conclusion**

The `Page` class is a lightweight and efficient way to generate HTML layouts for PHP-based websites. By automating common tasks like including meta tags, styles, and scripts, it allows developers to focus on building content and functionality.

If you have any suggestions or improvements, feel free to contribute!
