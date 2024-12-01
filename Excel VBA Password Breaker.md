### **Disclaimer**

Using tools or scripts to bypass password protection on files without proper authorization is unethical and, in many cases, illegal. This document is provided for educational purposes to understand how weak password protections can be exploited, encouraging the use of strong security measures. Always ensure you have the proper rights and permissions before attempting to bypass any protection.

---

# **Excel VBA Password Breaker**

This VBA script is a brute-force tool designed to attempt to unprotect an Excel worksheet by cycling through potential passwords. The method works by systematically generating combinations of characters and trying each as a password.

---

## **Code**

Here’s the VBA code:

```vba
Sub PasswordBreaker()
    ' Breaks worksheet password protection.

    Dim i As Integer, j As Integer, k As Integer
    Dim l As Integer, m As Integer, n As Integer
    Dim i1 As Integer, i2 As Integer, i3 As Integer
    Dim i4 As Integer, i5 As Integer, i6 As Integer

    On Error Resume Next ' Ignore errors to continue brute-forcing

    ' Nested loops to try all combinations of characters
    For i = 65 To 66
        For j = 65 To 66
            For k = 65 To 66
                For l = 65 To 66
                    For m = 65 To 66
                        For i1 = 65 To 66
                            For i2 = 65 To 66
                                For i3 = 65 To 66
                                    For i4 = 65 To 66
                                        For i5 = 65 To 66
                                            For i6 = 65 To 66
                                                For n = 32 To 126
                                                    ' Attempt to unprotect using the generated password
                                                    ActiveSheet.Unprotect Chr(i) & Chr(j) & Chr(k) & _
                                                    Chr(l) & Chr(m) & Chr(i1) & Chr(i2) & Chr(i3) & _
                                                    Chr(i4) & Chr(i5) & Chr(i6) & Chr(n)

                                                    ' Check if the sheet is unprotected
                                                    If ActiveSheet.ProtectContents = False Then
                                                        MsgBox "One usable password is " & Chr(i) & Chr(j) & _
                                                        Chr(k) & Chr(l) & Chr(m) & Chr(i1) & Chr(i2) & _
                                                        Chr(i3) & Chr(i4) & Chr(i5) & Chr(i6) & Chr(n)
                                                        Exit Sub
                                                    End If
                                                Next
                                            Next
                                        Next
                                    Next
                                Next
                            Next
                        Next
                    Next
                Next
            Next
        Next
    Next
End Sub
```

---

## **How It Works**

1. **Character Range**:
   - The script iterates over ASCII values from `32` (space) to `126` (tilde `~`), representing printable characters.
   - Letters (`A-Z` and `a-z`), numbers (`0-9`), and symbols are included.

2. **Nested Loops**:
   - The script uses deeply nested loops to generate combinations of characters. The password length depends on the depth of the loops.

3. **Unprotect Attempt**:
   - The script tries each generated password using `ActiveSheet.Unprotect`.

4. **Success Check**:
   - After each attempt, it checks if the sheet is no longer protected with `ActiveSheet.ProtectContents`.

5. **Output**:
   - If successful, the script displays a message box with the usable password.

---

## **Performance and Limitations**

1. **Brute Force**:
   - This is a brute-force approach. It may take an extremely long time to test all combinations, especially for longer passwords.

2. **ASCII Range**:
   - The script only works with printable ASCII characters (`32` to `126`). Passwords with extended Unicode characters will not be tested.

3. **Protection Strength**:
   - Modern Excel password protection is more sophisticated and this script may not work on newer versions.

---

## **How to Use**

1. **Enable Developer Mode**:
   - Open Excel.
   - Go to **File > Options > Customize Ribbon**.
   - Enable the **Developer** tab.

2. **Insert VBA Code**:
   - Open the workbook where you want to use the script.
   - Press `Alt + F11` to open the VBA editor.
   - Go to **Insert > Module** and paste the code into the module window.

3. **Run the Macro**:
   - Close the VBA editor and return to Excel.
   - Press `Alt + F8`, select `PasswordBreaker`, and click **Run**.

---

## **Legal and Ethical Considerations**

- **Authorized Use Only**:
  - Only use this script on files you own or have explicit permission to modify.
  
- **Alternative Solutions**:
  - If you’ve lost access to a protected sheet, consider reaching out to the file's creator or using authorized recovery tools.

- **Secure Practices**:
  - Use strong passwords and modern encryption to protect sensitive data.
  - Avoid relying on weak or default passwords.

---

## **Conclusion**

This script demonstrates the vulnerabilities of weak password protection in older versions of Excel. It’s a reminder to always use strong and complex passwords to safeguard your files. If you need help with securely managing or recovering your Excel sheets, consider modern tools and techniques.