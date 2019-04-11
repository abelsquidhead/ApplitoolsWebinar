# Quality in a DevOps World
This is the codebase I used for the webinar I did with Applitools. The demo app is a simple asp.net web app that I grabbed from a coworker. Things have been tweeked a bit but all in all, it's kind of a mess. Not good clean code at all. However, it does demo devops concepts well and one day, I may go in and rewrite everything so it's nice and clean.

In the mean time, the important parts in this code base is the testing project. It's here that I created my automated UI tests using Applitools Visual Testing API.

## Code for Visual Testing
The code for my visual tests reside in the /src/MercuryHealth.AutomatedTest directory. I wrote my tests using the Page Object pattern where every page in my application is represented by an object. This object has all the actions that can be performed on the page and also all the verifications that can be done for that page. All these methods return another page object. It can be itself if the action remains on the same page, or it will be a new page object representing the new page. This allows me to write super clean super easy to read tests in a fluent like manner.

Using the Page Object pattern also makes it MUCH easier to maintain my tests. If my page changes (which happens all the time), I just need to find page object, and make the changes there.

Hre is an exmaple of a test that shows how clean and easy it is to write. I'm using Microsoft's MSTest framework. However, you can easily use nUnit, xUnit or whatever framework you want:

``` c#
[TestMethod]
        [TestCategory("UITests")]
        public void Add1stDonutTest()
        {
            var webApp = HomePage.Launch(_applitoolsApiKey,
                                         _applitoolsBatchName,
                                         _applitoolsBatchId,
                                         "Add 1st Donut Test",
                                         _browserType);

            try
            {
                // browse to home page of app
                webApp.BrowseToHomePage(_homePageUrl)
                    .VerifyHomePageReached()
                    .TakeVisualPicture<HomePage>("Home Page")

                    //go to nutrition page
                    .ClickNutritionLink()
                    .VerifyNutritionPageReached()

                    // clean up and delete all donuts, take a picture of cleaned up nutrition page
                    .RemoveAllFood("Donut")
                    .TakeVisualPicture<NutritionPage>("Nutrition Page")

                    // click create new link to add new food
                    .ClickCreateNewLink()
                    .VerifyCreatePageReached()
                    .TakeVisualPicture<CreatePage>("Create Page")

                    // add donut as a food item and click the add button
                    .SetDescription("Donut")
                    .ClickCreateButton()
                    .VerifyNutritionPageReached()
                    .VerifyFoodInTable("Donut", 1)
                    .TakeVisualPicture<NutritionPage>("Nutrition Page After Adding Donut")

                    // clean up and delete all donuts 
                    .RemoveAllFood("Donut")
                    .TakeVisualPicture<NutritionPage>("Nutrition Paqge After Removing All Donuts");
            }
            catch (Exception e)
            {
                Assert.Fail("Exception occured during test run: " + e);
            }
            finally
            {
                webApp.Close();
            }
        }
```
In the test project, you will find a Pages folder. In there, I have all my page objects. The Test code itself is at /src/MercuryHealth.AutomatedTest/MercuryHealthAppTest.cs.

You will find a MercuryHealthAppTests.cs.old file. This shows what my tests looked like before adding applitools eyes.

Under Pages, you will also find BasePage.cs.old and HomePage.cs.old next to BasePage.cs and HomePage.cs. Those are just example files of what the page object looked like before adding applitools eyes.
