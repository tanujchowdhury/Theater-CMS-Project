# Theater-CMS Project

## Introduction
During my last two weeks at the Tech Academy, I was given the opportunity to work with a team on a full scale MVC web application in C#. The app was designed for a theater company to manage the various business functions such as productions, blogs, and rentals. My role was to create the [about page](#About-Page) for the website and handle the cast members in the 'productions' area. Along the way, I fixed bugs, refactored code, and added requested features. I also had an opportunity to work with a peer to fix issues with the database. During this live project I learned a lot of tricks in CSS, how to store [pictures](#Photo-Storage-and-Retrieval) in a database and render them on an HTML page, and developed a [search](#Search-Cast-Members-by-Production) feature that dynamically displays the results.

Below are the major stories I worked on. The About page story was front-end, while the photos and search were mainly back-end.

## Stories
* [About Page](#About-Page)
* [Photo Storage and Retrieval](#Photo-Storage-and-Retrieval)
* [Search Cast Members by Production](#Search-Cast-Members-by-Production)

### About Page
I was given a mock-up from a UI/UX student of webpage to replicate. Using CSS, I was able to make an exact replica of the mock-up. The two things I am proudest of were making the stylized corners of the sections and having the pictures on the screen look good in various window sizes. 

Below is my solution to make sweeping curved corners. I made a div in the HTML and then assigned properties to that with CSS. Issues I ran into were with alignment and spacing. Fixing them took trial error with margins and CSS precedence. The style tag in HTML allowed the About page to go the full width of the view port and eliminated unwanted horizontal scrolling.

```
<style>
    .body-content {
        width: 100vw;
        margin: 0;
        padding: 0;
        max-width: 100%;
        overflow-x: hidden;
    }
</style>

<div id="Home-about--curvedCornerTopRight"></div>
```

```
#Home-about--curvedCornerTopRight {
    width: 100px;
    height: 100px;
    overflow: hidden;
    position: relative;
    margin-left: auto;
    margin-right: 0;
}
#Home-about--curvedCornerTopRight:before {
    content: "";
    display: block;
    width: 200%;
    height: 200%;
    position: absolute;
    border-radius: 50%;
    top: 0;
    right: 0;
    box-shadow: 50px -50px 0 0 var(--main-color--light);
    margin-left: auto;
    margin-right: 0;
}
```

Part of the About page included pictues of the ensemble in rounded picture frames with their names underneath. Upon resing the page, the pictures would go out of alignment or not center properly on the page. My solution for this was to make image containers consiting of the image and the name and use vw in CSS to have it resize dynamically. I could handle the pieces separately in CSS and I ended up with a web-page that looked exactly like the customer wanted.

```
<div class="Home-about--imageContainer">
            <div class="Home-about--imageCenter">
                <img class="Home-about--rounded" src="~/Content/images/Cast_Img_7.jpg" alt="Tom Mounsey" />
            </div>
            <p class="Home-about--heading">TOM MOUNSEY</p>
        </div>
</div>
```

```
.Home-about--imageContainer {
    width: 15vw;
    display: inline-grid;
    text-align: center;
}
img.Home-about--rounded {
    object-fit: cover;
    border-radius: 50%;
    height: 10vw;
    width: 10vw;
    align-content: center;
}
.Home-about--imageCenter {
    text-align: center;
}
```

### Photo Storage and Retrieval
Part of my role in this project was handling everything to do with cast members. Part of the model was a photo of the cast member. My job was to allow the user to upload a photo, turn the photo into a byte array (byte[]) so it could be stored in the database, and then when requested the photo be rendered on the HTML webpage.
Below is a snippet from the POST method when creating a new cast member. The function has the photo passed in as a parameter and used BinaryReader to convert it to a byte array. In the snippets below, the if/else statements or try/catch blocks are included to handle errors in case the user does not upload a photo.

```
//Convert the image into bytes array type
if (postedFile != null)
{
    using (BinaryReader br = new BinaryReader(postedFile.InputStream))
    {
        bytes = br.ReadBytes(postedFile.ContentLength);
    }
}
else
{
    bytes = null;
}

castMember.Photo = bytes;       
```

When the user needs to see image in the index, details, and delete pages, the byte array needs to be converted back into a photo. To do this, I made a function that is called everytime a photo needs to be displayed.

```
public ActionResult RenderImage(int id)
{
    CastMember castMember = db.CastMembers.Find(id);

    byte[] byteData = castMember.Photo;

    try
    {
        return File(byteData, "image/png");
    }
    catch
    {
        return null;
    }          
}
```

```
<img src="@Url.Action("RenderImage", new { id = item.CastMemberId })" class="card-img CastMember-Index--image" />
```

### Search Cast Members by Production
When intially starting the project, the index page was a simple list of all the cast members. As I moved forward in the project, more features were requested. The cast members needed to be organized by production. Furthermore, if a search was done on the database that included no cast members from a given production, that entire production needed to be omitted from the view.

Below is the search feature. Only cast members with names or information in their bios that matched the search query, would show up on the index page.

```
// GET: Prod/CastMembers
// Search feature
public ActionResult Index(string search)
{            
    return View(db.CastMembers.Where(model => model.Name.StartsWith(search) || model.Bio.Contains(search) || search == null).ToList());           
}
```

```
@* Search bar *@
@using (Html.BeginForm("Index", "CastMembers", FormMethod.Get))
{
    <div class="float-right mb-4">
        @Html.TextBox("search")<input type="submit" name="submit" value="Search" class="CastMember-Index--search"/>
    </div>
}
```

This code makes a Bootstrap card-deck (cards will line up this way) for each unique production title in the database. Then a card is made for each cast member and sorted into the correct production. If a cast member should not be displayed due to the search parameters, it will not be sent to the index view to begin with. Null values for production titles are handled in the controller (not pictured) and changed to 'Other.' Then they are moved to the end of the list so that the 'Other' section will always appear last on the page.

```
@* Displays cast members in cards, sorted by ProductionTitle.
   Will not show ProductionTitle if no cast members found in search.
   'Other' section is moved to the end of page. *@
@if (Model.Count() == 0)
{
    <h2 class="display-4 mt-5">Records not found</h2>
}
else
{
    // get list of production titles
    var titles = Model.Select(m => m.ProductionTitle).Distinct();

    // move "Other" category to the end
    var others = titles.Where(x => x == "Other");
    var nonnulls = titles.Where(x => x != "Other");
    var result = nonnulls.Concat(others);

    // for each title, make a heading and card-deck
    foreach (var title in result)
    {
        <h2 class="display-4">@title</h2>
        <hr />

        <div class="card-deck">

            @* for each cast member in this production title, make a card *@
            @foreach (var item in Model)
            {
                if (item.ProductionTitle == title)
                {
                    <div class="card bg-dark CastMember-Index--card">
                        <img src="@Url.Action("RenderImage", new { id = item.CastMemberId })" class="card-img CastMember-Index--image" />
                        <div class="card-body">
                            <h5 class="card-title">@Html.DisplayFor(modelItem => item.Name)</h5>
                            <a href="@Url.Action("Edit", "CastMembers", new { id = item.CastMemberId })" class="btn btn-warning CastMember-Index--editBtn CastMember-Index--button">Edit</a>
                            <a href="@Url.Action("Delete", "CastMembers", new { id = item.CastMemberId })" class="btn btn-danger CastMember-Index--delBtn CastMember-Index--button">Delete</a>
                            <a href="@Url.Action("Details", "CastMembers", new { id = item.CastMemberId })" class="stretched-link"></a>
                            <p class="CastMember-Index--text">Click to see more!</p>
                        </div>
                    </div>
                }
            }
        </div>

        <hr />
        <hr class="CastMember-Index--greyLine">
        <hr />
    }
}
```

*[Jump to Top](#Theater-CMS-Project)*
