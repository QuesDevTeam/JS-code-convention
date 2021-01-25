# PLEASE READ THIS. It's not that long.
- Understand reasoning behind all these. Everything is simple, and should be simple.  
- Make it simple, but not too much. It's human who's going to read the code.  
- Try many things and learn from failure. Spend decent amount of time figuring out best code. You're fixing your brain, not the code.  
- Sometimes you have to accept what we have and carry on. But don't half ass the shit. Give it a several take before giving up. Leave some comment at least.

# Premise
- These rules are based on following esline plugins
  - eslint-config-airbnb
  - eslint-plugin-import
  - eslint-plugin-jsx-a11y
  - eslint-plugin-mocha
  - eslint-plugin-promise
  - eslint-plugin-react

# General
- Don't spam eslint-disable. Try to understand what's the problem is and conform.
- Be sure to re-enable disabled lint by ``` // eslint-enable ~~ ``` after disabling temporarily.
- Use 2 whitespace over \t.
- Use \n instead of \r\n.
- Single/Double quote
  - Use double quote for human-readable string.
  - Otherwise use single quote.
- TODO, FIXME, XXX
  - ```// TODO: We should implement something later on.```
  - ```// FIXME: There's problem with this code. Fix somehow.```
  - ```// XXX: This portion should be fixed/removed before release.```

- Case
  - Use camelCase when naming objects, functions, and instances. [example](https://github.com/airbnb/javascript#naming--camelCase)
  - Use PascalCase only when naming constructors or classes. [example](https://github.com/airbnb/javascript#naming--PascalCase)

# Variable declaration:
- Declare one variable per line
- const, let, var
  - Follow eslint. Always prefer ```const``` over ```let```.
  - Never use ```var```.
- Instance: camel hump, starts with lower case
- Constant: UPPER_CASE_WITH_UNDERSCORE
- Use parentheses("(\~\~)") when using "&&" and "||" together, or doing complicated calculation. [example](https://github.com/airbnb/javascript#comparison--no-mixed-operators)
- Assign variables where you need them, but place them in a reasonable place. [example](https://github.com/airbnb/javascript#variables--define-where-used)
- Object: Declare every k-v pair in separate line.

# Function:
- Do one thing, and do it well.
  - No side effect. Modify something that function name says to do.
- Camel hump, starts with lower case UNLESS it's a class
- No unnamed function(eslint)
- Empty space between ')' and '{'. See [here](https://github.com/airbnb/javascript#functions--signature-spacing)
- No deconstructing object inside function argument declaration
  ```js
  // Bad
  function someFunctionName({ propertyOne, propertyTwo }) {}

  // Good
  function someFunctionName(someObject) {}
  ```
- Gather & split code batch into logical group
  ```js
  async function createElectionResult(election) {
    const electionResultBody = await createPollResult(election);

    if (!isElectionOpenableRegardlessOfRate(election)) {
      assertPollByVoteRate(electionResultBody.population, electionResultBody.totalVoteCount);
    }

    const voteCountsPerCandidate = await getVoteCountsPerCandidate(election._id, election.candidates);
    electionResultBody.voteCountsPerCandidate = voteCountsPerCandidate;

    if (election.isYesOrNo) {
      electionResultBody.againstVotesCount = await pollRateController
        .countAgainstVotesFromElection(election._id);
    }

    return mongodbController.saveModel(ElectionResult, electionResultBody);
  }
  ```
- Don't mutate parameter unless it's absolutely necessary.
  - If there's no trivial way to do it, name function to show what it's doing.
  - Also, return mutated parameter to make it even more obvious.
  ```js
  export default async function setupApp() {
    let app = express();

    app = setup(app);
    app = configPassport(app);
    app.use('/api', routers);
    app = configDynamicPages(app);
    app = addMiddlewares(app);
    app = handleError(app);

    return app;
  }
  ```
- Don't return unnecessary value.
  ```js
  async function authUser(accountId, password, done) {
    const account = await Account.findOne({ accountId });

    if (!account) {
      // Bad
      return done(null, false, { message: `There is no admin account "${accountId}"` });
    }

    const success = await account.comparePassword(password);

    if (!success) {
      // Good
      done(null, false, { message: 'Wrong password' });
      return;
    }

    // Bad
    // return done(null, account);

    // Good
    done(null, account);
  }
  ```

# Comparison
- Use triple equal(===, !==) over double equal(==, !=)
- Use shortcuts for booleans, but explicit comparisons for objects, strings and numbers. [example](https://github.com/airbnb/javascript#comparison--shortcuts)
- Ternaries should not be nested and generally be single line expressions. [example](https://github.com/airbnb/javascript#comparison--nested-ternaries)

# Conditionals
  - if\~else / switch
    - Exhaustive if\~else/case
      - Use if\~else instead of if\~if when it makes sense.
    - Make sure to write as close as english sentence
      ```js
      // Bad
      if (someThreshold < valueToExamine)

      // Good
      if (valueToExamine > someThreshold)
      ```
    - To early-exit or not to early-exit(developing)
  - for: Prefer built-in function(forEach, filter, map, reduce) over plain for loop when possible.

# Async-Await & Promise(developing)
- Prefer using async/await over returning Promise.
  ```js
  async function test(options) {
    const someResult = await someFunction(options);

    return someResult;
  }

  // Bad
  function doSomething() {
    const options = { count: undefinedVariable.length };
    return test(options);
  }

  doSomething().then(() => console.log("Some message")).catch((_) => console.error("Error occurred."));
  // Output:
  //  ReferenceError: undefinedVariable is not defined at doSomething

  // Good
  async function doSomething() {
    const options = { count: undefinedVariable.length };
    const result = await test(options);

    return result;
  }

  doSomething().then(() => console.log("Some message")).catch((_) => console.error("Error occurred."));
  // Output:
  //  Error occurred.
  ```
- Do not return any value inside Promise.<sup>[1](#no-promise-executor-return)</sup>
  ```js
  // Bad
  new Promise((resolve, reject) => {
    if (someCondition) {
        return defaultResult;
    }

    getSomething((err, result) => {
        if (err) {
            reject(err);
        } else {
            resolve(result);
        }
    });
  });

  // Good
  new Promise((resolve, reject) => {
    if (someCondition) {
        resolve(defaultResult);
        return;
    }

    getSomething((err, result) => {
        if (err) {
            reject(err);
        } else {
            resolve(result);
        }
    });
  });
  ```

# Import & Export
## export
- Prefer integrated export.
  ```js
  // bad
  export default asdf;
  export zxcv;
  export function qwer() {}

  // good
  export {
    asdf as default,
    zxcv,
    qwer,
    ...
  };
  ```

## import
- Use ES6 style import
  - Refrain using "require"
    - Exception: Some packages(ex. OracleDB ~4.0.1) don't support ES6 import.
- import order: system > library > models > controllers > utility > constants
  ```js
    // system
    import fs from 'fs';
    import path from 'path';
    // libraries
    import express from 'express';
    import moment from 'moment';
    // models
    import Voter from '../../models/voter';
    import Admin, { ViewerAdmin, SubAdmin } from '../../models/admin';
    // controllers
    import pollController from '../../controllers/api/poll';
    import voterController from '../../controllers/api/voter';
    import pollRateController from '../../controllers/api/pollRate';
    import pollResultController from '../../controllers/api/pollResult';
    import permissionController from '../../controllers/api/permission';
    // utilities
    import {
      // ... some methods ...
    } from '../../utils/router';
    import QuesError from '../../utils/error';
    // constants
    import ACTIONS from '../../resources/actions.json';
  ```
- Imports from single file must be executed by single import
  ```js
  // bad
  import foo from 'foo';
  import { named1, named2 } from 'foo';

  // good
  import foo, { named1, named2 } from 'foo';
  ```
- Exclude file extension
  ```js
  // bad
  import foo from '../foo.js';
  
  // good
  import foo from '../foo';
  ```
## Detail rules per type of module
- For models(or if there's something including or representing whole file's concept)
  - export model as default
    ```js
    // export
    export {
      Vote as default,
    }

    export default Vote;

    // import
    import Vote from '/path/to/model';
    ```
  - export by-products(ex. discriminated models)
    ```js
    // export
    export {
      Vote as default,
      OpenVote,
    }

    export default Vote;
    export { Open }

    // import
    import Vote, { OpenVote } from '/path/to/model';
    ```
- For controllers(or bunch of functions for specific feature's)
  ```js
  // export
  export default {
    submitVotes,
    getVotes,
    deleteVotes,
    verifyVotes,
  };

  // import
  import voteController from '/path/to/controller';
  ```
- For utilities(or something for common usage)
  ```js
  // export
  export {
    argumentsExists,
    argumentsExistsPromise,
    fallback,
    or,
    exists,
    range,
    under,
    toString,
    toInt,
    isValidMongoDbId,
    isFunction,
  };

  // import
  import { exists, toString } from '/path/to/util';
  ```

# References
- https://github.com/felixge/node-style-guide
- https://github.com/airbnb/javascript#naming--uppercase

# Notes
- <a name="no-promise-executor-return">Do not return any value inside Promise</a>: This rule is defined in [eslint](https://eslint.org/docs/rules/no-promise-executor-return), but for some reason I couldn't make it to work. Please make a PR whever was able to fix this.
