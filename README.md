# Theater-CMS Project

## Introduction
During my last two weeks at the Tech Academy, I was given the opportunity to work with a team on a full scale MVC web application in C#. The app was designed for a theater company to manage the various business functions such as productions, blogs, and rentals. My role was to create the about page for the website and handle the cast members in the 'productions' area. Along the way, I fixed bugs, refactored code, and added requested features. I also had an opportunity to work with a peer to fix issues with the database. During this live project I learned a lot of tricks in CSS, how to store pictures in a database and render them on an HTML page, and developed a search feature that dynamically displays the results.

Below are the major stories I worked on. The About page story was front-end, while the photos and search were mainly back-end.

## Stories
* About Page
* Photo Storage and Retrieval
* Search Cast Members by Production

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

Part of the About page included pictues of the ensemble in rounded picture frames with their names underneath. Upon resing the page, the pictures would go out of alignment or not center properly on the page. My solution for this was to make image containers consiting of the image and the name and use vw in CSS to have it resize dynamically. I could the handle each piece separately in CSS and I ended up with a web-page that looked exactly like the customer wanted.

```
<div class="Home-about--imageContainer">
            <div class="Home-about--imageCenter">
                <img class="Home-about--rounded" src="~/Content/images/Cast_Img_7.jpg" alt="Tom Mounsey" />
            </div>
            <p class="Home-about--heading">TOM MOUNSEY</p>
        </div>
</div>

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
 
