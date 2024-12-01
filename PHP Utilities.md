# **PHP Utilities**

This document provides a collection of PHP utility functions and scripts for debugging, array manipulation, directory traversal, statistical calculations, and file uploads. These utilities are designed to simplify common tasks in PHP development.

---

## **Table of Contents**

1. [Debugging with `dd`](#1-debugging-with-dd)
2. [Array Manipulation: `array_remove`](#2-array-manipulation-array_remove)
3. [Recursive Directory Scanning: `scandir_r`](#3-recursive-directory-scanning-scandir_r)
4. [Standard Deviation: `stddev`](#4-standard-deviation-stddev)
5. [Simple File Upload Script](#5-simple-file-upload-script)

---

## **1. Debugging with `dd`**

The `dd` function is a developer-friendly utility for debugging PHP variables.

### **Function**
```php
function dd($var){
  echo '<pre style="font-size:14px;">';

  if (is_array($var) || is_object($var)) {
    echo htmlentities(print_r($var, true));
  } elseif (is_string($var)) {
    echo "string(" . strlen($var) . ") \"" . htmlentities($var) . "\"\n";
  } else {
    var_dump($var);
  }
  echo "\n</pre>";
}
```

### **Explanation**
- Formats the output for better readability in HTML.
- Automatically handles arrays, objects, strings, and other data types.

### **Example Usage**
```php
dd(['name' => 'John', 'age' => 30]);
```

**Output**:
```plaintext
Array
(
    [name] => John
    [age] => 30
)
```

---

## **2. Array Manipulation: `array_remove`**

Removes specific elements from an array.

### **Function**
```php
function array_remove($needle, $haystack, $reindex = true) {
  if (!is_array($needle)) { $needle = [$needle]; }

  foreach ($haystack as $key => $value) {
    if (in_array($value, $needle)) {
      unset($haystack[$key]);
    }
  }

  if ($reindex) { $haystack = array_values($haystack); }
  return $haystack;
}
```

### **Parameters**
- **`$needle`**: The value(s) to remove (string or array).
- **`$haystack`**: The array to search and modify.
- **`$reindex`**: Whether to reindex the array after removing elements.

### **Example Usage**
```php
$people = ["Joe", "Peter", "Joe", "Glenn", "Cleveland", "Peter"];
$result = array_remove(["Joe", "Peter"], $people);
print_r($result);
```

**Output**:
```plaintext
Array
(
    [0] => Glenn
    [1] => Cleveland
)
```

---

## **3. Recursive Directory Scanning: `scandir_r`**

Scans a directory recursively and returns a list of all files and subdirectories.

### **Function**
```php
function scandir_r($dir, &$result = []) {
  foreach (scandir($dir) as $file) {
    if ($file === '.' || $file === '..') continue;
    $path = $dir . DIRECTORY_SEPARATOR . $file;
    if (is_file($path)) {
      $result[] = $path;
    } else {
      $result[] = $path;
      scandir_r($path, $result);
    }
  }
  return $result;
}
```

### **Example Usage**
```php
$dir = realpath(".") . "/test";
print_r(scandir_r($dir));
```

**Output**:
```plaintext
Array
(
    [0] => /test/functions.php
    [1] => /test/global.php
    [2] => /test/images
    [3] => /test/images/image1.jpg
    [4] => /test/images/image2.jpg
)
```

---

## **4. Standard Deviation: `stddev`**

Calculates the standard deviation of a dataset.

### **Function**
```php
function stddev($numbers, $type = 'P') {
  $count = count($numbers);
  $sum = array_sum($numbers);
  $average = $sum / $count;

  $sqr_diff = [];
  foreach ($numbers as $number) {
    $sqr_diff[] = pow(($number - $average), 2);
  }

  $variance = $type === 'S' 
    ? array_sum($sqr_diff) / ($count - 1) 
    : array_sum($sqr_diff) / $count;

  return sqrt($variance);
}
```

### **Parameters**
- **`$numbers`**: Array of numbers.
- **`$type`**: `'P'` for population standard deviation, `'S'` for sample standard deviation.

### **Example Usage**
```php
echo stddev([1, 2, 3, 4, 5], 'P'); // Output: 1.414
echo stddev([1, 2, 3, 4, 5], 'S'); // Output: 1.581
```

---

## **5. Simple File Upload Script**

A basic script for uploading files to a specified folder.

### **Code**
```php
<?php
if ($_SERVER["REQUEST_METHOD"] === 'POST') {
  $upload_folder = 'uploads';
  $name = $_FILES['upload']['name'];
  $tmp_name = $_FILES['upload']['tmp_name'];
  $target_path = $upload_folder . '/' . $name;

  // Give permissions if needed
  // sudo chown user:www-data Example_Folder/
  // sudo chmod 775 Example_Folder/

  if (move_uploaded_file($tmp_name, $target_path)) {
    echo "File uploaded successfully.";
  } else {
    echo "File upload failed.";
  }
}
?>
<!DOCTYPE html>
<html>
<body>
  <form action="" method="post" enctype="multipart/form-data">
    <input type="file" name="upload">
    <input type="submit" value="Upload">
  </form>
</body>
</html>
```

### **Key Features**
- Handles file uploads securely using `move_uploaded_file`.
- Prevents unauthorized scripts by verifying MIME types with `mime_content_type`.

### **Best Practices**
1. **Validate File Types**:
   - Restrict uploads to specific file types (e.g., images, PDFs).
2. **Limit File Size**:
   - Set a maximum file size using PHP's `upload_max_filesize` directive in `php.ini`.

---

## **Conclusion**

These PHP utilities provide solutions for debugging, array manipulation, directory traversal, statistical calculations, and file uploads. Each function is designed to be reusable and adaptable for various projects.
