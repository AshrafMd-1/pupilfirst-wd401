# Error Tracking System
## What is Error Tracking
Error tracking, also known as error monitoring or bug tracking, is the process of identifying, capturing, recording, and managing errors or issues that occur within software applications or systems. Errors can range from minor glitches to critical malfunctions that impede the functionality or usability of the software.

## Why Do We Need Error Tracking
- **Identification**: Quickly pinpoint issues in software.
- **Prioritization**: Organize and address errors based on severity.
- **Resolution**: Facilitate the process of identifying and fixing bugs.
- **Monitoring**: Continuously monitor for recurring errors.
- **Reporting**: Provide insights into error trends and resolution effectiveness.
## What is Sentry
Sentry is an error tracking software platform designed to help developers monitor, detect, and fix errors in their applications. It provides real-time error tracking, allowing teams to identify and prioritize issues efficiently. With features like stack trace data, error aggregation, and alerting capabilities, Sentry enables developers to diagnose and resolve issues quickly, leading to improved application reliability and user experience.
## Use Sentry in Project
1. First select a project type ![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/d52ee57b-7173-4291-bc0a-493e5cc45426)
2. Now sent the alert timer and the project timer ![[Pasted image 20240320202032.png]]
3. Now open project terminal and install `npm install --save @sentry/react` ![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/9948cccb-d6c3-40be-83af-26c0e2649a9b)
4. Now save this file in the `main.tsx` file ![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/2f66a57b-8217-4793-9d4b-3705ae4f52ca)
5. If now there are any errors then you will get this kind of errors ![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/05c36f8d-178b-40e3-9660-e1a39c4c091a)
This is how you sent up sentry for automatic logging of errors
# Debugger Capability

## Understanding Debugger Capability in React

In React development, debugging is essential for identifying and fixing issues that arise during the creation and testing of your application.

- **React DevTools:** This browser extension is specifically designed for inspecting React components. It offers a visual representation of the component hierarchy, allows examination of state and props, and provides debugging tools like breakpoints. ![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/80faee4a-0ddc-4c99-bcbf-baeb99e4c617)

- **Browser Developer Tools:** Every modern browser includes built-in developer tools with features like the Console (for logging messages and inspecting variables), Sources (for viewing and editing code), and Network (for analyzing network requests and responses). ![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/6692d85b-3200-4ba9-85b4-a57e4c32440c)

## Implementing Debugging Capabilities

1. **Install React DevTools:**

    - Open your browser's extension store (e.g., Chrome Web Store).
    - Search for "React DevTools" and install the extension.
2. **Enable Source Maps:**

    - Source maps create a bridge between your minified production code and the original source code for better debugging.
    - Follow your build setup's instructions for enabling source maps in development mode.

## Sample Code (Counter App)

JavaScript

```
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  const increment = () => setCount(count + 1);
  const decrement = () => setCount(count - 1);

  return (
    <div>
      <h1>Count: {count}</h1>
      <button onClick={increment}>Increment</button>
      <button onClick={decrement}>Decrement</button>
    </div>
  );
}

export default Counter;
```

**Debugging Techniques**

1. **Inspecting Component Hierarchy and State:**

    - Open your browser's developer tools and navigate to the React DevTools tab.
    - You'll see a visual representation of your components, including the `Counter` component.
    - Click on the component to view its state (the current `count` value). ![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/cb4e126a-f1fa-45c4-bb19-513dbb6bd3a2)

2. **Setting Breakpoints:**

    - In your code editor, set a breakpoint on a line of code you want to pause execution at (e.g., inside the `increment` function).
    - When you run the app and click the "Increment" button, the code will pause at the breakpoint.
    - This allows you to inspect variables and the component's state at that specific point in time. ![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/602c8449-5a0b-4937-b2b1-7091c840d815)
3. **Stepping Through Code and Inspecting Variables:**

    - In your browser's developer tools, once a breakpoint is hit, navigate to the Sources tab.
    - Use the debugger controls (e.g., step over/into/out) to execute code line by line.
    - The Console and Call Stack panels show variable values and the function call sequence.
    - This enables you to examine how variables change as the code executes and track down issues.
4. **Logging Messages:**

    - Use `console.log` statements throughout your code to print values to the Console, helping you track data flow and identify potential errors.
    - Be cautious of leaving too many `console.log` statements in production code, as they can impact performance.

## Sample Debugging Report

**Issue:** The counter isn't incrementing correctly when you click the "Increment" button.

**Steps:**

1. **Inspected Component Hierarchy:** Confirmed that the `Counter` component is rendering correctly.
2. **Set Breakpoint:** Placed a breakpoint in the `increment` function on the line `setCount(count + 1)`.
3. **Clicked "Increment" Button:** Code paused at the breakpoint.
4. **Inspected Variables:** Checked that `count` had the expected value.
5. **Stepped Through Code:** Examined the value of `count` after the `setCount` call in the next line.
6. **Identified Issue:** Discovered a logic error causing the `count` value not to be updated correctly.

**Resolution:** Fixed the code in the `increment` function to ensure accurate incrementing.
