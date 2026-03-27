# 🔐 Implementing API Security (Subscriber REST API)

## 📌 Scenario
Structureality has hired you as a cybersecurity expert to assess and secure their **Subscriber REST API endpoint**.  

Your mission is to:
- Analyze API behavior  
- Identify vulnerabilities  
- Perform security scans  
- Implement secure coding practices  
- Re-test to validate remediation  

---

## 🎯 Objectives
- Explore REST API using Swagger & Postman  
- Perform API vulnerability scanning (Wapiti3 & W4AF)  
- Identify OWASP Top 10 issues  
- Implement API security best practices  
- Validate remediation through rescanning  

---

## 🖥️ Lab Environment
- **Kali Linux VM**
- **Node.js API Server (localhost:3000)**
- Tools:
  - Visual Studio Code
  - Postman (Scratch Pad)
  - Swagger UI
  - Wapiti3
  - W4AF

---

# 🚀 TASK 1: Setup & Explore API

## 📂 Step 1: Locate API Files
```bash
ls -la
cd node-api
🖼️ Screenshot: Directory Listing

🧑‍💻 Step 2: Open API in VS Code
code .
🖼️ Screenshot: VS Code Project View

⚙️ Step 3: Run API Server
npx nodemon server.js
🖼️ Screenshot: Server Running

🌐 Step 4: Access Swagger

Open browser:

http://localhost:3000/api-docs
🖼️ Screenshot: Swagger UI

🧪 Step 5: Test API (GET)
Expand GET /api/items
Click Try it out → Execute
🖼️ Screenshot: Swagger GET Request

🧪 TASK 2: Postman API Testing
📌 Create Requests
🔹 POST Request
POST http://localhost:3000/api/item

Body:

{
  "name": "John Doe",
  "subscribedToChannel": "Bitcoin"
}
🔹 PATCH Request
PATCH http://localhost:3000/api/items/1
🔹 DELETE Request (VULNERABLE)
DELETE http://localhost:3000/api/item/2

⚠️ Security Issue:
No authentication → Anyone can delete data.

🔹 GET Request
GET http://localhost:3000/api/items
🖼️ Screenshot: Postman Requests

💾 Export Results
Save as:
API-Post.json
API-Patch.json
🔍 TASK 3: Vulnerability Scanning
🐍 WAPITI Scan
cd wapiti3-3.2.0
. wapiti3/bin/activate
python wapiti3/bin/wapiti -u http://localhost:3000/api/items -m all
🖼️ Screenshot: Wapiti Scan

⚠️ Findings
Missing Content Security Policy
Header vulnerabilities
Potential XSS
💾 Save Report
Wapiti_scan.html
🧰 W4AF Scan
cd ~/w4af
pipenv install
pipenv shell
./w4af_console
Configure:
target
set target http://localhost:3000/api/items
save

plugins
audit cors_origin, un_ssl, xss, xxe
Output:
output html_file
set output_file ~/Downloads/W4AF_cors.html
save

start
🖼️ Screenshot: W4AF Scan

🚨 Vulnerabilities Found
CORS misconfiguration
XSS
Missing security headers
No rate limiting
No authentication
🛡️ TASK 4: Remediation (Secure API)
✅ 1. Environment Variables
const dotenv = require('dotenv');
dotenv.config();
✅ 2. Security Headers (Helmet)
const helmet = require('helmet');
app.use(helmet());
✅ 3. Logging & Monitoring
const winston = require('winston');
const morgan = require('morgan');

app.use(morgan('combined', {
  stream: winston.stream.write
}));
🔥 CHALLENGE IMPLEMENTATIONS
✅ 4. Enable CORS
const cors = require('cors');
app.use(cors());
✅ 5. Rate Limiting
const rateLimit = require('express-rate-limit');

app.use(rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100
}));
✅ 6. Prevent Parameter Pollution
const hpp = require('hpp');
app.use(hpp());
✅ 7. Prevent XSS
const xss = require('xss-clean');
app.use(xss());
🔁 TASK 5: Re-Scan After Fix
python wapiti3/bin/wapiti -u http://localhost:3000/api/items -m all
🖼️ Screenshot: Rescan Results

✅ Improvements Observed
Security headers added
Reduced vulnerabilities
XSS mitigated
API hardened
📊 Key Security Lessons
Never expose APIs without authentication
Always validate inputs
Use middleware for protection
Monitor & log traffic
Regularly scan APIs
