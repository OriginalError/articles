# Code Gremlins and Testing

## Have you heard the legend of the code gremlin? 

The code gremlin is a mythical creature that can change your production code without your consent - but like all good legendary creatures he has a weakness. The code gremlin cannot change production code if it leads to a test failure. Which leads you to a fun question - do you trust your tests enough to let a code gremlin play in your source code? Or - phrased a different way - do we trust our tests enough to let Josh the Junior-Dev refactor our codebase? For most teams the answer is no, of course not. Have you met Josh?

Besides – performing small changes to run tests against would be prohibitively expensive. Every possible permutation of code would take hundreds of engineer and tester hours. It would increase in complexity exponentially with code base size. This testing would need to be repeated after any change. 

...but what if you could automate testing your tests? Mutation testing is just that - fully automated code gremlins that change your code to validate your automated testing. These small changes are called `Mutants.` 

## How does it work?

### Explanation
Mutation testing works by making these small changes. Each individual change is called a mutant. How does that work under the hood? You can see some examples of mutants in the chart below:

| Original | Mutated | Description |
| -------- | ------- | ----------- |
| a * b    | a / b   | Arithmetic Operations Mutant - Reverse the operation occurring |
| new Array(1, 2, 3) | new Array() | Array Declaration Mutant - Remove any initialization of arrays |
| false | true | Boolean Literal Mutant - Invert the Boolean literal |
| function foo() { bar; } | function foo() {} | Block Statement Mutant - Erase all functionality inside of a function |

Each of these mutants is one very small change like the examples above. After exactly one mutation is introduced your entire testing suite is run again. You `kill` the mutant if one of your tests fails (or if the testing suite times out). If a mutant *doesn’t* cause a test to fail - the mutant survives. If the mutant is not covered by the tests – the mutant survives. These surviving mutants expose how the code gremlin could sabotage us.

It will show you where code isn’t tested or is insufficiently tested. Code coverage alone can give us a false sense of confidence with how safely we can elevate - by incorporating mutation testing into our SDLC we can see covered code that is untested with meaningful assertions. Let’s look at a [contrived example](https://github.com/OriginalError/contrivedMutationExample) with 100% code coverage.

### Example
#### Code Snippet
```Javascript
function(str) {
  //null quick exit  
  if (isString(str)) return "";

  //Chunk that string into words
  var arr = str.match(/\w[a-z]{0,}/gi);

  //Empty string (or completely numeric string) quick exit
  if (hasWords(arr)) return "";

  var result = arr[0];
  for(var x = 1; x < arr.length; x++) {
    if(result.length < arr[x].length) {
      result = arr[x];
    } 
  }
  return result;
}
```

#### Test Snippet
```Javascript
  it('should return the longest word', function(done) {
    var sentence = 'Fake people have an image to maintain. Real people just don’t care.';
    stringFormatter.longestWord(sentence);
    done();
  });
  
  it('should return an empty string when an empty string is provided', function(done) {
    var result = stringFormatter.longestWord('');
    assert.strictEqual(result, '');
    done();
  });

  it('should return an empty string for non-string input', function(done) {
    var result = stringFormatter.longestWord(1337);
    assert.strictEqual(result, '');
    done();
  });
```

#### Coverage
![Picture of Coverage](https://i.imgur.com/PJkseb3.png "Coverage")

#### Mutation
![Picture of Mutation](https://i.imgur.com/ws5iD4e.png "Mutants")

 In this mutation testing run we can clearly see despite the code having 100% coverage - we had mutants that lived. This means that if you only looked at code coverage – you would assume these lines were tested! If we look at the CLI output - it will list every single mutant that was introduced and survived.

 #### Night of the Living Mutants
 ![Picture of Living Mutants](https://i.imgur.com/MnLKYgO.png "Living Mutants")

 You can see in the output of the surviving mutant - Stryker informs you the type of mutant it was and provides instructions to modify your code to "reproduce" that mutant.

## Benefits

Mutation testing can find weakly tested code by manufacturing code gremlin activity, this can allow us to design additional tests to cover weakly tested code. We might also see that no defined behavior required for the code to exist in the first place! Identifying code that doesn’t provide any business value can reduce the size of your code base.

Mutation testing can find weak testing. If a test kills no mutants - what is it testing? We can analyze tests that don’t kill *any* mutants to determine if they’re worth the maintenance of keeping.

Mutation testing can provide you with a better quantification of test quality than code coverage. By comparing the ratio of mutants killed to the total number - you get an excellent idea of how much code *is* completely covered by your tests.

## Silver Bullet?

Keep in mind mutation testing is another tool for your development toolbox. We all know that there is no silver bullet for good tests (even if there was... silver bullets are for werewolves, not mutants).

## Additional Reading

Java Mutation Testing: https://pitest.org/  
Javascript/C/Scala Mutation Testing: https://stryker-mutator.io/ 
