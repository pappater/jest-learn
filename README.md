## **Jest Notes**

### **Basic Concepts**

Some links for reference: 
1. https://jestjs.io/docs/tutorial-react
2. https://www.atlassian.com/continuous-delivery/software-testing/types-of-software-testing

1. **What is Jest?**

   - A JavaScript testing framework used to test code by writing test cases. It’s popular for its simplicity and built-in features.

2. **Installation**

   - Install Jest using npm or yarn:
     ```bash
     npm install --save-dev jest
     ```
     ```bash
     yarn add --dev jest
     ```

3. **Basic Test**

   - Create a test file (e.g., `sum.test.js`):

     ```javascript
     // sum.js
     function sum(a, b) {
       return a + b;
     }
     module.exports = sum;

     // sum.test.js
     const sum = require("./sum");

     test("adds 1 + 2 to equal 3", () => {
       expect(sum(1, 2)).toBe(3);
     });
     ```

4. **Running Tests**

   - Run tests using the command:
     ```bash
     npm test
     ```
     or
     ```bash
     yarn test
     ```

5. **Assertions**
   - `toBe(value)` - Checks for exact equality.
   - `toEqual(value)` - Checks for deep equality.

### **Intermediate Concepts**

1. **Mocking**

   - Mock functions simulate the behavior of real functions to test interactions:
     ```javascript
     const mockFn = jest.fn();
     mockFn("arg1");
     expect(mockFn).toHaveBeenCalledWith("arg1");
     ```

2. **Setup and Teardown**

   - Use `beforeEach`, `afterEach`, `beforeAll`, `afterAll` to run code before/after tests:

     ```javascript
     beforeEach(() => {
       // Code to run before each test
     });

     afterEach(() => {
       // Code to run after each test
     });
     ```

3. **Asynchronous Testing**

   - Use `done` callback or `async/await` for async code:
     ```javascript
     test("async test", async () => {
       const data = await fetchData();
       expect(data).toBe("expectedData");
     });
     ```

4. **Snapshot Testing**

   - Capture a snapshot of a component's output and compare it to future snapshots:

     ```javascript
     import renderer from "react-test-renderer";
     import MyComponent from "./MyComponent";

     test("renders correctly", () => {
       const tree = renderer.create(<MyComponent />).toJSON();
       expect(tree).toMatchSnapshot();
     });
     ```

5. **Coverage Reporting**
   - Generate test coverage reports using:
     ```bash
     npm test -- --coverage
     ```
     or
     ```bash
     yarn test --coverage
     ```

### **Advanced Concepts**

1. **Custom Matchers**

   - Extend Jest with custom matchers to assert more complex conditions:

     ```javascript
     expect.extend({
       toBeEven(received) {
         const pass = received % 2 === 0;
         if (pass) {
           return {
             message: () => `${received} is even`,
             pass: true,
           };
         }
         return {
           message: () => `${received} is not even`,
           pass: false,
         };
       },
     });

     test("number is even", () => {
       expect(4).toBeEven();
     });
     ```

2. **Test Hooks**

   - Use `beforeEach`, `afterEach`, `beforeAll`, `afterAll` for setup/teardown of tests. They can be asynchronous:

     ```javascript
     beforeAll(async () => {
       await setupDatabase();
     });

     afterAll(async () => {
       await cleanupDatabase();
     });
     ```

3. **Parameterized Tests**

   - Use `test.each` for running the same test with different data:
     ```javascript
     test.each([
       [1, 2, 3],
       [4, 5, 9],
     ])("sum of %i and %i is %i", (a, b, expected) => {
       expect(sum(a, b)).toBe(expected);
     });
     ```

4. **Manual Mocks**

   - Create a `__mocks__` directory to manually mock modules:
     ```javascript
     // __mocks__/module.js
     module.exports = {
       myFunction: jest.fn(() => "mocked value"),
     };
     ```

5. **Test Isolation**
   - Ensure each test is isolated and does not depend on others by using mock functions and clearing state.

Additional Jest concepts not covered in the notes:

### **Additional Concepts**

1. **Testing React Components**

   - Use `@testing-library/react` to render and interact with components:

     ```bash
     npm install --save-dev @testing-library/react
     ```

     ```javascript
     import { render, screen } from "@testing-library/react";
     import MyComponent from "./MyComponent";

     test("renders component", () => {
       render(<MyComponent />);
       expect(screen.getByText("Hello, World!")).toBeInTheDocument();
     });
     ```

2. **Mocking Modules**

   - Mock entire modules to control their behavior in tests:

     ```javascript
     jest.mock("axios");
     import axios from "axios";

     axios.get.mockResolvedValue({ data: "mocked data" });

     test("fetches data", async () => {
       const data = await fetchData();
       expect(data).toBe("mocked data");
     });
     ```

3. **Timers and Time Mocks**

   - Control timers in tests with `jest.useFakeTimers()`:

     ```javascript
     jest.useFakeTimers();

     test("delays execution", () => {
       const callback = jest.fn();
       setTimeout(callback, 1000);

       jest.advanceTimersByTime(1000);
       expect(callback).toHaveBeenCalled();
     });
     ```

4. **Spies**

   - Use `jest.spyOn` to spy on functions without completely mocking them:
     ```javascript
     const myFunction = jest.spyOn(obj, "method");
     obj.method();
     expect(myFunction).toHaveBeenCalled();
     ```

5. **Parallel and Sequential Test Execution**

   - Run tests in parallel (default) or sequentially with `--runInBand`:
     ```bash
     npm test -- --runInBand
     ```
     - Running tests sequentially is useful for tests that share state.

6. **Mocking Implementations**

   - Provide specific implementations for mocked functions:
     ```javascript
     const mockFn = jest.fn().mockImplementation(() => "mocked return");
     expect(mockFn()).toBe("mocked return");
     ```

7. **ES Modules and Jest**

   - Configure Jest to work with ES modules by setting `"type": "module"` in `package.json` and adjusting Jest settings:
     ```json
     {
       "jest": {
         "transform": {}
       }
     }
     ```

8. **Handling Errors in Tests**

   - Use `toThrow` to test if a function throws an error:

     ```javascript
     function throwError() {
       throw new Error("Test error");
     }

     test("throws an error", () => {
       expect(() => throwError()).toThrow("Test error");
     });
     ```

9. **Testing with Enzyme (Legacy)**

   - If you're working with older React projects, you might encounter Enzyme. Install and use it for component testing:

     ```bash
     npm install --save-dev enzyme enzyme-adapter-react-16
     ```

     ```javascript
     import { shallow } from "enzyme";
     import MyComponent from "./MyComponent";

     test("renders without crashing", () => {
       shallow(<MyComponent />);
     });
     ```

10. **Environment Variables**

    - Use `process.env` to test code that relies on environment variables:

      ```javascript
      process.env.API_URL = "http://mockapi.com";

      test("uses API URL", () => {
        expect(getApiUrl()).toBe("http://mockapi.com");
      });
      ```

11. **Running Only Specific Tests**

    - Use `.only` to run a single test or group of tests:

      ```javascript
      test.only("this is the only test that will run", () => {
        // Test code here
      });

      describe.only("only this group will run", () => {
        // Grouped test cases here
      });
      ```

12. **Skipping Tests**

    - Use `.skip` to skip specific tests or groups of tests:

      ```javascript
      test.skip("this test will be skipped", () => {
        // Test code here
      });

      describe.skip("this group of tests will be skipped", () => {
        // Grouped test cases here
      });
      ```

13. **Debugging Tests**

    - Use `test.only` with `console.log` statements or `debugger` to isolate and debug tests.

14. **Custom Test Environment**

    - Set up a custom test environment (e.g., for Node.js or browser-like environments):
      ```javascript
      module.exports = {
        testEnvironment: "node",
      };
      ```

15. **Watch Mode**

    - Use Jest’s watch mode to automatically re-run tests when files change:
      ```bash
      npm test -- --watch
      ```

16. **Test Retry**
    - Retry failing tests automatically with Jest's retry feature:
      ```javascript
      test.retry(3)("test with retry", () => {
        // Test code here
      });
      ```
