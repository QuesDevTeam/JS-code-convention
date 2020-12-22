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

# Base .eslintrc.json
```json
{
    "env": {
        "browser": true,
        "es6": true,
        "node": true,
        "mocha": true
    },
    "extends": [
        "airbnb",
        "plugin:promise/recommended",
        "plugin:mocha/recommended"
    ],
    "globals": {
        "Atomics": "readonly",
        "SharedArrayBuffer": "readonly"
    },
    "parserOptions": {
        "ecmaFeatures": {
            "jsx": true
        },
        "ecmaVersion": 2018,
        "sourceType": "module"
    },
    "plugins": [
        "react",
        "promise",
        "mocha"
    ],
    "rules": {
        // One true brace style
        // https://eslint.org/docs/rules/brace-style.html
        "brace-style": [2, "1tbs", { "allowSingleLine": false }],
        // Require following curly brace always
        "curly": ["error", "all"],
        // Don't use single character naming
        "id-length": ["error", { "exceptions": ["i", "_"] }],
        // No method chaining more than 2
        "newline-per-chained-call": ["error", { "ignoreChainWithDepth": 2 }],
        "no-multiple-empty-lines": ["error", { "max": 1 }],
        // Maximum line per line
        "max-len": ["error", { "code": 100 }],
        "no-else-return": "off",
        "no-use-before-define": "off",
        "no-console": ["error", { "allow": ["info", "warn", "error"] }],
        "no-unused-vars": ["error", { "argsIgnorePattern": "^_$" }],
        "promise/always-return": "off",
        "no-underscore-dangle": ["error", { "allow": ["_id", "__v", "_gridId"] }],
        "no-plusplus": ["error"]
    }
}
```

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
- Prefer async-await over promise

# Import & Export(developing)
- export: Let someone else to do this section... See below:
  - https://github.com/airbnb/javascript#modules
  - https://github.com/airbnb/javascript#naming--filename-matches-export
  - https://github.com/airbnb/javascript#naming--camelCase-default-export
  - https://github.com/airbnb/javascript#naming--PascalCase-singleton
  - https://github.com/airbnb/javascript#naming--Acronyms-and-Initialisms
  - https://github.com/airbnb/javascript#naming--uppercase
- es6 style import
- refrain using "require"
- import order: system - library - controllers - utility

# References
- https://github.com/felixge/node-style-guide
- https://github.com/airbnb/javascript#naming--uppercase
