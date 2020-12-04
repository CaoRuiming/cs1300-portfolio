# Development Project

This project started out as a declaritive UI programming exercise (using the [React.js](https://reactjs.org) framework) to create a simple frontend application in [React.js](https://reactjs.org) that would actually be useful to a student like me.

The application itself is a simple "Shopping Cart" implementation themed around 1000-level computer science (CS) courses offered at Brown University. All course information in this repository were pulled from the [Courses@Brown](https://cab.brown.edu) website and [The Critical Review](https://thecriticalreview.org/). 

## Goal & Functionality of Application

The goal of this application is to help users pick and choose from a set of 1000-level CS courses at Brown while also keeping track of the estimated weekly commitment required in hours. Thus, users can easily plan which computer science courses to take in a semester without being surprised by an unmanageable workload.

![Application screenshot](imgs/application.png)

Users can view a set of 12 CS courses and choose which ones to add to their "course shopping cart". The cart adds together the total weekly hours of each added course and conveniently displays the number to the user.

To see the final deployed product, [click on this link](https://pacific-sea-06829.herokuapp.com).

To see the source code for the app, [click on this link](https://github.com/CaoRuiming/cs1300-development-app).

## Usability Principles Considered

To make my application intuitive and pleasant to use, I kept the following usability principles in mind:
- Learnability
  - There is space between the shopping cart and the course gallery with the viewing options to keep these two sections distinct.
  - No potentially confusing icons are used in the interface: buttons linked to specific actions contain descriptive text  (e.g. 'Add to Cart' button).
  - Interface layout is similar to the typical one-page shopping site, so users can easily trans
  - Clicking any non-disabled button in the interface will result in an immediate action which can be undone if desired (e.g. filtering, sorting, adding and removing items from cart).
- Memorability
  - Courses can only appear in the shopping cart once, so once an item is added to the cart, the 'Add to Cart' button is disabled and changed to say 'Added to Cart'.
- Efficiency
  - Useful filter and sort options are included in the view options section above the course gallery so that users can more easily find courses that fit into their schedules.

So, without further ado, let's dive into the juicy details!

## Technical Details

All source code is [available on Github](https://github.com/CaoRuiming/cs1300-development-app).

This section outlines the architecture of this project and includes the following details:
- Organization of components
- How data is passed down through components
- How user interactions can trigger changes in the state of components

### Component Organization/Hierarchy

When going into this project, I wanted my code to have a relatively flat structure to stay as concise and easy to understand.

There are 3 main components:
- **App**: the root component of the app; contains all logic involving user interaction (i.e. sorting, filtering, and adding/removing items)
- **Gallery**: a component that displays a list of courses as a collection of cards; this component is used for both displaying all avaiable courses as well as displaying all items in the user's shopping cart
- **ViewOptions**: a component that contains buttons that allow the user to filter and sort the items in the course selection gallery (not shopping cart)

The hierarchy is as follows:
- `App`
  - left section div
    - `ViewOptions`
    - `Gallery` (Course Selection)
  - right section div
    - `Gallery` (Shopping Cart)

Below is a screenshot of the app again for reference.

![Application screenshot](imgs/application.png)

### Flow of Data Between Components

`App` holds all of the state values and state-setting functions. The state values and setters are shown below:

```js
const [semesterFilter, setSemesterFilter] = useState(SEMESTER.ALL);
const [trackFilter, setTrackFilter] = useState(TRACK.ALL);
const [sortBy, setSortBy] = useState(SORT.DEFAULT);
const [courseGalleryItems, setCourseGalleryItems] = useState(ITEMS);
const [cartGalleryItems, setCartGalleryItems] = useState([]);
```

(Syntax highlighting is powered by [Prism](https://prismjs.com/index.html))

`App` only passes the necessary values and functions to its child components:
- `ViewOptions` receives the values and setters for both filters and sortBy as props from `App`
- `Gallery` (Course Selection) receives courseGalleryItems and a function for adding courses to the shopping cart as props from `App`
- `Gallery` (Cart Selection) receives cartGalleryItems and a function for removing courses from the shopping cart as props from `App`

### Triggering Actions Through User Interactions

UI changes are all triggered by the setter functions: setSemesterFilter, setTrackFilter, setSortBy, setCourseGalleryItems, and setCartGalleryItems.

#### `ViewOptions`
Whenever the user clicks on a filter or sort method in `ViewOptions`, the corresponding setter function is called, triggering a `useEffect` hook in `App` to filter, sort, and update courseGalleryItems. This updated list of items is then passed into the `Gallery` (Course Selection) component to update the displayed course cards.

In case you're interested in how the `useEffect` hook does the sorting and filtering, here it is:
```js
// update displayed courses in the course gallery upon filter or sort change
useEffect(() => {
  // start out with copy of original full course list
  let newCourseGalleryItems = [...ITEMS];

  // apply filters
  newCourseGalleryItems = newCourseGalleryItems
    .filter(x => semesterFilter === SEMESTER.ALL || x.semester === semesterFilter)
    .filter(x => trackFilter === TRACK.ALL || x.track === trackFilter);

  // apply sorting
  if (sortBy !== SORT.DEFAULT) {
    newCourseGalleryItems = newCourseGalleryItems
      .sort((a, b) => {
        return sortBy === SORT.HOURS_ASC
          ? a.weeklyHours - b.weeklyHours
          : b.weeklyHours - a.weeklyHours
      });
  }

  // save new list of course items
  setCourseGalleryItems(newCourseGalleryItems);
}, [
  semesterFilter,
  trackFilter,
  sortBy,
  setSemesterFilter,
  setTrackFilter,
  setSortBy,
  setCourseGalleryItems,
]);
```

(Syntax highlighting is powered by [Prism](https://prismjs.com/index.html))

#### `Gallery` (Course Selection)
When the user clicks on the add button for any course card, it triggers a callback function that appends that course item to cartGalleryItems in `App`, which then is sent to `Gallery` (Cart Selection) to update the items displayed in the cart.

#### `Gallery` (Cart Selection)
When the user clicks on the remove button for any item in the shopping cart, it triggers a callback function that removes that course item from cartGalleryItems in `App`, which is sent back to `Gallery` (Cart Selection) to update the items displayed in the cart.

## Conclusion

This project overall was fun exercise in concise and descriptive React.js development. Although the scope of the application's current form is rather limited in terms of content and filtering/sorting, the structure of this project should make future extensions or spinoffs easy to do.