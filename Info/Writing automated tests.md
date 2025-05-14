# Automated Testing

It's possible to automate testing your plugin. This will speed up your development process, since you can execute your test whenever you make a change, and it will only take a few seconds.

In order to write an automated test, put the following code into a header file in your plugin:

```cpp
namespace SystemTests
{
    // This is a UUID, you'll need to generate a new one in this format
    // {087B4192-DFDD-4B59-9264-1F96D349936D}
    static const VWIID IID_YourTestID = { 0x87b4192, 0xdfdd, 0x4b59,{ 0x92, 0x64, 0x1f, 0x96, 0xd3, 0x49, 0x93, 0x6d } };

    using namespace VectorWorks::Debug;
    class YourTest : public VCOMImmediateImpl<ISystemTest>
    {
        CPPUNIT_TEST_SUITE2(YourTest, "YourTestName", "your.email@domain.net", ESystemTestType::SpecialRun);
        CPPUNIT_TEST(test_YourFunction, "YourFunction");  // You can have as many of these as you want
        CPPUNIT_TEST_SUITE_END();

        void setUp();
        void tearDown();

        void test_YourFunction();
    };
}
```

And then put this code into a source file:

```cpp
void SystemTests::YourTest::setUp()
{
    // This opens a blank new document
    CPPUNIT_ASSERT(gSDK->OpenDocumentPath(nullptr, false));
}

void SystemTests::YourTest::tearDown()
{
    // This closes the current document
    CPPUNIT_ASSERT(gSDK->CloseDocument() == false);
}

void SystemTests::YourTest::test_YourFunction()
{
    // Call your function and assert its result
    int a = 2;
    int b = 2;
    int c = YourFunction(a, b);
    CPPUNIT_ASSERT(c == 4);    
}
```

In your `ModuleMain.cpp`, you'll need to include your header and add this line to your `plugin_module_main` function:

```cpp
REGISTER_SystemTest<SystemTests::YourTest>( SystemTests::IID_YourTestID, action, moduleInfo, iid, inOutInterface, cbp, reply );
```

`setUp()` and `tearDown()` will be called before and after the functions passed into the `CPPUNIT_TEST` macro. Use these to open test files, initialize variables, and properly close them after your test is finished.

`CPPUNIT_ASSERT` checks an expression for true or false. Use this to check that your code works the way you intended it to. If it fails, then the line and file name will be printed to the output.

## Running Automated Tests

When you run Vectorworks, open the Debug menu and select **System Test Manager**. Look for your test name, then click to the left of it to bring up a check mark. Then click the **Run** button, and the output will show up in the "Output" field.

You can also run automated tests from the command line by passing in these arguments to `Vectorworks.exe`:

```bash
-t YourTestName -ab -l path/to/your/test/output.txt
```

You can pass in multiple tests like this:

```bash
-t Test1 -t Test2
```

You can also pass in a text file with test names on new lines:

```bash
-t testList.txt  
```

## Testing Best Practices

- **Isolate a piece of functionality.** It’s better to call the function you’re interested in directly rather than trying to simulate menu actions or button clicks. If you can eliminate unnecessary variables, your test won’t fail for other reasons besides what you’re intending to test.

- **Think carefully about what this function is supposed to do.** Don’t assert things that aren’t a direct product of the function getting called. We don’t want to assert side effects or things that are incidental to the function’s purpose. This is one of the main benefits of testing: it forces you to think about your design and nail down exactly how everything should work.

- **Try to break it.** After you make sure it works in all the normal use cases, if you have time you should throw weird edge cases in there too. Assume that someone is going to use this function completely wrong. Does it fail cleanly, or does it crash Vectorworks?

- **Test the most frequently used code paths.** What part of your code is going to get used most frequently by your customers? Focus your testing time on that.

- **Check your assumptions.** What assumptions does the code you’re testing make? Are you completely sure that those assumptions are always valid? If you test that assumption in your automated test, if there’s ever a time when that assumption doesn’t work anymore, the test will notify you.
