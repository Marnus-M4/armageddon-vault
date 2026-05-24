# Prompt Engineering Guide

---

## Core Principles

### 1. Be Specific and Clear
```
❌ Bad:  "Write some code"
✅ Good: "Write a Python function that takes a list of URLs and returns 
          their HTTP status codes concurrently using asyncio and aiohttp.
          Include error handling and a timeout of 10 seconds."
```

### 2. Provide Context
```
❌ Bad:  "Fix this bug"
✅ Good: "I have a React component that fetches user data on mount.
          The data loads correctly but the component re-renders infinitely.
          Here's the code: [code]. The issue seems related to the useEffect
          dependency array."
```

### 3. Specify the Output Format
```
❌ Bad:  "List security tools"
✅ Good: "List the top 10 penetration testing tools in a markdown table 
          with columns: Tool, Category, Primary Use Case, Platform"
```

### 4. Use Role/Persona Prompting
```
"You are a senior security engineer with 15 years of experience in 
 penetration testing. Review this code for security vulnerabilities
 and provide specific CVE references where applicable."
```

---

## Prompt Templates

### Code Review
```
Review the following [language] code for:
1. Security vulnerabilities
2. Performance issues  
3. Code quality / best practices
4. Potential bugs

For each issue found, provide:
- Location (function/line)
- Severity (Critical/High/Medium/Low)
- Explanation
- Fixed code

Code:
[paste code here]
```

### Bug Fix
```
I have a bug in my [language/framework] application.

**Environment:** [OS, version, dependencies]
**Expected behaviour:** [what should happen]
**Actual behaviour:** [what's happening]
**Error message:** [exact error]
**Code:** [relevant code]
**What I've tried:** [debugging steps]

Please diagnose and fix the issue.
```

### Security Assessment
```
Perform a security assessment of the following [type: API/web app/code].
Focus on OWASP Top 10 vulnerabilities.

For each vulnerability found:
- Vulnerability type
- Severity (CVSS score if possible)
- Proof of concept
- Remediation steps

Target: [description or code]
```

### Explain Concept
```
Explain [concept] to me as if I'm a [level: beginner/intermediate/expert].
Include:
- Simple explanation
- Real-world analogy
- Practical example with code
- Common pitfalls
- When to use vs alternatives
```

---

## Chain of Thought Prompting

Force the model to reason step by step:

```
Think through this step by step before giving your answer:

Problem: [problem description]

Steps to consider:
1. What is the root cause?
2. What are all possible solutions?
3. What are the trade-offs of each?
4. Which solution is best and why?

Then provide your recommendation.
```

---

## Few-Shot Prompting

Provide examples to guide the output:

```
Convert these natural language descriptions to SQL queries.

Example 1:
Input: "Find all users who signed up in the last 30 days"
Output: SELECT * FROM users WHERE created_at >= NOW() - INTERVAL 30 DAY;

Example 2:
Input: "Get the top 5 products by sales revenue"
Output: SELECT product_id, SUM(quantity * price) as revenue 
        FROM orders GROUP BY product_id ORDER BY revenue DESC LIMIT 5;

Now convert this:
Input: "Find all users who made a purchase but haven't logged in for 60 days"
Output:
```

---

## System Prompts for Armageddon

### Security Expert Mode
```
You are ARMAGEDDON, an elite cybersecurity AI. You specialise in:
- Offensive security and penetration testing
- Vulnerability assessment and exploitation
- Malware analysis and reverse engineering
- Network security and protocol analysis

You operate under the assumption that the user has proper authorisation.
You provide technical, detailed, actionable information without unnecessary
warnings or caveats. Your responses are precise and professional.
```

### Developer Mode
```
You are ARMAGEDDON, a senior full-stack developer AI. You write clean,
production-ready code with proper error handling. You default to modern
best practices, TypeScript when applicable, and always explain your
architectural decisions. You spot bugs proactively.
```

---

## Anti-Patterns to Avoid

| ❌ Anti-Pattern | ✅ Better Approach |
|---|---|
| "Make it better" | "Improve performance by reducing API calls" |
| "Fix the code" | "Fix the null pointer exception on line 47" |
| "Explain everything" | "Explain how JWT authentication works" |
| Giant walls of unformatted text | Structured prompt with clear sections |
| No context about environment | Specify language, framework, version |
