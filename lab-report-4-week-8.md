# Lab Report 4

## Markdown Parse Snippets
---

My Markdown-Parse repository is [this](https://github.com/notSaranshMalik/markdown-new) one.

The Markdown-Parse repository of the person I reviewed is [this](https://github.com/ZhuoyangM/markdown-parse) one.

### Snippet 1
Based on CommonMark, the expected links for this snippet should be:
* `google.com
* google.com
* ucsd.edu

As such, I wrote my test as:
```
@Test
public void testSnip1() throws IOException {
    Path fileName = Path.of("snip1.md");
    String contents = Files.readString(fileName);
    ArrayList<String> links = MarkdownParse.getLinks(contents);
    assertEquals(List.of("`google.com", "google.com", "ucsd.edu"), links);
}
```

For my implementation, the test failed, with the JUnit output as follows:
```
testSnip1(MarkdownParseTest)
java.lang.AssertionError: expected:<[`google.com, google.com, ucsd.edu]> but was:<[url.com, `google.com, google.com]>
    at org.junit.Assert.fail(Assert.java:89)
    at org.junit.Assert.failNotEquals(Assert.java:835)
    at org.junit.Assert.assertEquals(Assert.java:120)
    at org.junit.Assert.assertEquals(Assert.java:146)
    at MarkdownParseTest.testSnip1(MarkdownParseTest.java:55)
```
My implementation could not find the "ucsd.edu" link due to the doubled up closing bracket, but had the additional "url.com" link since it didn't check for code blocks.

For the implementation I reviewed, the test failed, with the JUnit output as follows:
```
testSnip1(MarkdownParseTest)
java.lang.AssertionError: expected:<[`google.com, google.com, ucsd.edu]> but was:<[url.com, `google.com, google.com, ucsd.edu]>
    at org.junit.Assert.fail(Assert.java:89)
    at org.junit.Assert.failNotEquals(Assert.java:835)
    at org.junit.Assert.assertEquals(Assert.java:120)
    at org.junit.Assert.assertEquals(Assert.java:146)
    at MarkdownParseTest.testSnip1(MarkdownParseTest.java:65)
```
The implementation included the additional "url.com" link since it didn't check for code blocks.

### Snippet 2
Based on CommonMark, the expected links for this snippet should be:
* a.com
* a.com(())
* example.com

As such, I wrote my test as:
```
@Test
public void testSnip2() throws IOException {
    Path fileName = Path.of("snip2.md");
    String contents = Files.readString(fileName);
    ArrayList<String> links = MarkdownParse.getLinks(contents);
    assertEquals(List.of("a.com", "a.com(())", "example.com"), links);
}
```

For my implementation, the test failed, with the JUnit output as follows:
```
testSnip2(MarkdownParseTest)
java.lang.AssertionError: expected:<[a.com, a.com(()), example.com]> but was:<[a.com, a.com((]>
    at org.junit.Assert.fail(Assert.java:89)
    at org.junit.Assert.failNotEquals(Assert.java:835)
    at org.junit.Assert.assertEquals(Assert.java:120)
    at org.junit.Assert.assertEquals(Assert.java:146)
    at MarkdownParseTest.testSnip2(MarkdownParseTest.java:63)
```
My implementation could not find the correct "a.com(())" link due to the link terminating at the first closing parenthases, and couldn't find the "example.com" link since it ignored escaping characters.

For the implementation I reviewed, the test failed, with the JUnit output as follows:
```
testSnip2(MarkdownParseTest)
java.lang.AssertionError: expected:<[a.com, a.com(()), example.com]> but was:<[a.com, a.com((, example.com]>
    at org.junit.Assert.fail(Assert.java:89)
    at org.junit.Assert.failNotEquals(Assert.java:835)
    at org.junit.Assert.assertEquals(Assert.java:120)
    at org.junit.Assert.assertEquals(Assert.java:146)
    at MarkdownParseTest.testSnip2(MarkdownParseTest.java:73)
```
The implementation could not find the correct "a.com(())" link due to the link terminating at the first closing parenthases.

### Snippet 3
Based on CommonMark, the expected links for this snippet should be:
* https://ucsd-cse15l-w22.github.io/

As such, I wrote my test as:
```
@Test
public void testSnip3() throws IOException {
    Path fileName = Path.of("snip3.md");
    String contents = Files.readString(fileName);
    ArrayList<String> links = MarkdownParse.getLinks(contents);
    assertEquals(List.of("https://ucsd-cse15l-w22.github.io/"), links);
}
```

For my implementation, the test failed, with the JUnit output as follows:
```
testSnip3(MarkdownParseTest)
java.lang.AssertionError: expected:<[https://ucsd-cse15l-w22.github.io/]> but was:<[
    https://www.twitter.com
, 
    https://ucsd-cse15l-w22.github.io/
, github.com

And there's still some more text after that.

[this link doesn't have a closing parenthesis for a while](https://cse.ucsd.edu/



]>
    at org.junit.Assert.fail(Assert.java:89)
    at org.junit.Assert.failNotEquals(Assert.java:835)
    at org.junit.Assert.assertEquals(Assert.java:120)
    at org.junit.Assert.assertEquals(Assert.java:146)
    at MarkdownParseTest.testSnip3(MarkdownParseTest.java:71)
```
My implementation took all the multiline, line break including, unfinished links as proper links, and did not strip the whitespace out of the 1 proper link it should have found.

For the implementation I reviewed, the test failed, with the JUnit output as follows:
```
testSnip3(MarkdownParseTest)
java.lang.AssertionError: expected:<[https://ucsd-cse15l-w22.github.io/]> but was:<[]>
    at org.junit.Assert.fail(Assert.java:89)
    at org.junit.Assert.failNotEquals(Assert.java:835)
    at org.junit.Assert.assertEquals(Assert.java:120)
    at org.junit.Assert.assertEquals(Assert.java:146)
    at MarkdownParseTest.testSnip3(MarkdownParseTest.java:81)
```
The implementation could not find any links at all since it had a specfic check for whitespace in a link, which does not always mean a link isn't valid.

### Questions
_Do you think there is a small (<10 lines) code change that will make your program work for snippet 1 and all related cases that use inline code with backticks? If yes, describe the code change. If not, describe why it would be a more involved change._

Searching for "](" instead of just "]" and then "(" would fix the error my program had for not finding the last link, and seems like something that could be fixed by actually just changing 2 lines of code. This seems very simple to implement. The other error was that it had an extra link, because it didn't realise part of the link was a code block. This might be a little harder to fix since you need to check for code blocks, but also for newline characters (since those end code blocks), which might make this fix a bit harder. That said, it's certainly doable but would require a lot of checks for starting and ending code blocks.

_Do you think there is a small (<10 lines) code change that will make your program work for snippet 2 and all related cases that nest parentheses, brackets, and escaped brackets? If yes, describe the code change. If not, describe why it would be a more involved change._

This one would be a lot harder to fix since links with parentheses in them seem to not end the link in markdown. As such, the program would have to check to make sure it's the last parenthases on the line, while also checking there's no whitespace (since that ends links), while also ignoring escaped characters. This would be harder to fix since there would need to be checks in many places to check to make sure every single character we look for (like brackets and parentheses) are not escaped. 

_Do you think there is a small (<10 lines) code change that will make your program work for snippet 3 and all related cases that have newlines in brackets and parentheses? If yes, describe the code change. If not, describe why it would be a more involved change._

This seems much easier to fix. All I would need to do is make sure there's no double newline character inside the brackets or parentheses, and then strip all other whitespace out of a link before adding it to the final array. This is a fix you could implement in less than 10 lines of code.