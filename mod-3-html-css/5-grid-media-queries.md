# 5. Grid & Media Queries

{% hint style="info" %}
Follow along with code examples [here](https://github.com/The-Marcy-Lab-School/3-5-grid-media-queries)!
{% endhint %}

**Table of Contents**:

* [Key Concepts](5-grid-media-queries.md#key-concepts)
* [Grid vs Flexbox](5-grid-media-queries.md#grid-vs-flexbox)
* [Grid Template Columns](5-grid-media-queries.md#grid-template-columns)
  * [Fractional Units (`fr`) and `repeat()`](5-grid-media-queries.md#fractional-units-fr-and-repeat)
  * [Grid Template Columns Quiz](5-grid-media-queries.md#grid-template-columns-quiz)
  * [Controlling Row Height with `grid-auto-rows`](5-grid-media-queries.md#controlling-row-height-with-grid-auto-rows)
* [Responsive Web Design & Media Queries](5-grid-media-queries.md#responsive-web-design--media-queries)
  * [Breakpoints](5-grid-media-queries.md#breakpoints)
  * [Flexbox Media Query Challenge](5-grid-media-queries.md#flexbox-media-query-challenge)
* [Grid Alignment](5-grid-media-queries.md#grid-alignment)
  * [Justify Content (Horizontal Spacing)](5-grid-media-queries.md#justify-content-horizontal-spacing)
  * [Align Items (Vertical Alignment)](5-grid-media-queries.md#align-items-vertical-alignment)
  * [Centering Items in a Grid](5-grid-media-queries.md#centering-items-in-a-grid)
* [Going Further: Spanning Rows and Columns](5-grid-media-queries.md#going-further-spanning-rows-and-columns)

## Key Concepts

* **CSS Grid** is a `display` type that is used to arrange elements in two dimensions (rows and columns).
  * The **grid container** is the parent element with the `display: grid` property
  * **Grid items** are any elements inside of the grid container.
* `grid-template-columns` defines for the grid container the number of columns and the width of each column.
  * The `fr` unit (the "fractional unit") is unique to grid. It evenly distributes the available space in the grid container to each grid item without overflowing.
  *   To create evenly sized columns, we often use the `repeat()` function like this:

      ```css
      .grid-container {
        /* three equal columns */
        grid-template-columns: repeat(3, fr);
      }
      ```
*   **Grid tracks** are lines that indicate the start and end of a column. For example, grid track 1 and grid track 2 outline the first column.

    ![a grid with grid tracks](<../.gitbook/assets/grid-span-with-tracks (1).png>)
* **Responsive Web Design** is a strategy for creating websites that adapt their layout and content to ensure usability across devices of all sizes (phones, tablets, desktops, etc...).
  * **Media queries** allow us to apply CSS _in response_ to changes to the device's screen size (and other things too!).
  * **Breakpoints** are specific screen sizes at which a responsive web design will shift. We define them in CSS using media queries `@media (min-width)` and `@media(max-width)`
  * **Mobile-First Design** is a responsive web design strategy that starts with designing for mobile devices first and using media queries to modify that design for progressively larger screens

**Mobile-First Responsive Design Example Using Grid and Media Queries:**

```css
.grid-container { 
  display: grid;
  /* The default grid is a single column */
}


/* Small devices (landscape phones, 576px and up) */
@media (min-width: 576px) {
  .grid-container {
    grid-template-columns: 1fr 1fr;
  }
}

/* Medium devices (tablets, 768px and up) */
@media (min-width: 768px) {
  .grid-container {
    grid-template-columns: repeat(3, 1fr);
  }
}

/* Large devices (desktops, 992px and up) */
@media (min-width: 992px) {
  .grid-container {
    grid-template-columns: repeat(4, 1fr);
  }
}
```

## Grid vs Flexbox

We've learned that Flexbox is a `display` type that allows us to arrange items in single dimension: horizontally or vertically.

**Grid** on the other hand is a `display` type that allows us to arrange items in _two_ dimensions: rows and columns.

![Flexbox lets us make one dimensional layouts while grid lets us make two-dimensional layouts](../.gitbook/assets/flex-vs-grid.png)

<details>

<summary><strong>Q: Consider the gallery in the image below. It is made using grid. How many rows and columns are there?</strong></summary>

There are 5 columns and 5 rows

In a grid, grid items can span across multiple rows and/or columns

</details>

![An example of a website using grid to make a photo gallery. Photos can span across rows and columns](<../.gitbook/assets/grid-photo-gallery-example (1).png>)

## Grid Template Columns

By default, a grid will have only 1 column. We can define the number of columns and their sizes using `grid-template-columns`:

```css
.grid-container { 
  display: grid;
  grid-template-columns: 100px 200px; 
}
```

This will make 2 columns. The first will be `100px` wide, the second will be `200px` wide.

Above we are specifying an exact column width using the `px` unit. Even if there is more space available in the parent container, the columns won't grow or shrink to use it.

### Fractional Units (`fr`) and `repeat()`

If we want our columns to grow and shrink with the page, we can use the `fr` unit which is equal to one fraction of the _available_ space.

```css
.grid-container { 
  display: grid;
  grid-template-columns: 1fr 2fr; 
}
```

Above, the first column gets 1 fraction of the space and the second gets 2 fractions. In other words, the first column gets one third (1/3) of the space and the second gets two thirds (2/3).

To create four equal columns, just give them each `1fr`:

```css
.grid-container { 
  display: grid;
  /* make 4 equally sized columns that take up all available space */
  grid-template-columns: 1fr 1fr 1fr 1fr; 
}
```

Think of it like dividing a pizza: if you have 4 columns each set to `1fr`, each column gets 1 slice of the total available width, so they are all equal (all 1/4 of the space).

We can simplify this using the `repeat()` function:

```css
.grid-container { 
  display: grid;
  /* make 4 equally sized columns that take up all available space */
  grid-template-columns: repeat(4, 1fr) 
}
```

### Grid Template Columns Quiz

![](../.gitbook/assets/template-columns.png)

<details>

<summary><strong>Q: There are 9 elements but only 4 columns. What do you notice happens when the elements overflow? How is the row height determined?</strong></summary>

* If there are more elements than columns, they will flow into a new row that is created automatically.
* By default, row height is determined by the largest row item.
* Use `grid-auto-rows: 1fr;` to make all rows have equal size.

</details>

<details>

<summary><strong>Q: How would you adjust this to make columns of different sizes? For example, make the middle two columns twice as wide as the outer two.</strong></summary>

We can make columns of different sizes by adjusting the relative `fr` units. To make the middle columns twice as wide as the outer columns, use `2fr` instead of `1fr`

```css
grid-template-columns: 1fr 2fr 2fr 1fr; /* make 4 equal-sized columns */
```

</details>

### Controlling Row Height with `grid-auto-rows`

When grid items overflow into new rows, those rows are created automatically. By default, each row's height is determined by its tallest item—which can result in uneven rows.

The `grid-auto-rows` property lets you control the height of these automatically-created rows:

```css
.grid-container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-auto-rows: 200px; /* All rows will be exactly 200px tall */
}
```

**Common values for `grid-auto-rows`:**

| Value   | Behavior                                                                              |
| ------- | ------------------------------------------------------------------------------------- |
| `200px` | Fixed height — all rows are exactly 200px                                             |
| `1fr`   | Equal height — all rows share the available space equally                             |
| `40vh`  | Responsive height — rows are 40% of the viewport width (great for square grid items!) |

Using viewport units like `vw` is particularly useful for photo galleries where you want images to maintain a consistent aspect ratio as the screen resizes.

## Responsive Web Design & Media Queries

Head over to YouTube and play around with the size of your window. You should notice that the grid of videos will change its layout as the screen shrinks and grows!

![The number of columns increases as the screen size increases.](../.gitbook/assets/media-queries-example.gif)

This is an example of **responsive web design** which is a strategy for creating websites that adapt their layout and content to ensure usability across devices of all sizes (phones, tablets, desktops, etc...).

Responsive web design is implemented using **media queries** which allow programmers to specify CSS rules that are applied based on the device's screen size.

Open the example in `3-media-queries/index.html` and notice how the background color of each box changes as the size increases due to the following code:

```css
@media (min-width: 600px) {
  .box {
    background: plum;
  }
}

@media (min-width: 700px) {
  .box {
    background: orchid;
  }
}
```

* The @media keyword starts a new media query
* The `(min-width: 600px)` syntax indicates the minimum width for a screen to apply those styles. Any screen that is 600px or wider will apply those styles.
* Inside the media query, we use normal CSS selectors to define rules.

**Challenge**: At `700px` add a second column. At `800px` to set the background to `mediumpurple` and text color to `white`. At `900px` add a third column.

<details>

<summary>Solution</summary>

To set the number of grid columns to 2 at 700 pixels, we need to add a second ruleset to the media query.

```css
@media (min-width: 700px) {
  .box {
    background: orchid;
  }
  .container {
    grid-template-columns: 1fr 1fr;
  }
}

@media (min-width: 800px) {
  .box {
    background: mediumpurple;
    color: white;
  }
}

@media (min-width: 900px) {
  .container {
    grid-template-columns: 1fr 1fr 1fr;
  }
}
```

</details>

### Breakpoints

**Breakpoints** are points at which a responsive web design will shift. Below are commonly agreed upon breakpoints for web development. They don't target every specific use case or device, but the ranges provide broad coverage.

| Breakpoint                         | Dimensions |
| ---------------------------------- | ---------- |
| X-Small Devices (portrait phones)  | < 576px    |
| Small Devices (landscape phones)   | ≥ 576px    |
| Medium Devices (tables)            | ≥ 768px    |
| Large Devices (desktops)           | ≥ 992px    |
| X-Large Devices (large desktops)   | ≥ 1200px   |
| XX-Large Devices (larger desktops) | ≥ 1400px   |

### Flexbox Media Query Challenge

Using media queries and the `display: flex` properties, achieve the responsive design below starting with the code found in `4-responsive-flexbox-challenge/`

![](../.gitbook/assets/flexbox-media-query-challenge.png)

<details>

<summary><strong>Q: What do you notice if different about the two layouts?</strong></summary>

* Mobile view: navigation links are in a row and are above the `main`
* Desktop view: navigation links are in a column and are to the side of `main`

</details>

## Grid Alignment

Good news: **alignment in Grid works just like Flexbox!** You already know `justify-content` and `align-items` from the Flexbox lesson. These properties work the same way in Grid, with one key difference:

| Property          | Flexbox                               | Grid                                         |
| ----------------- | ------------------------------------- | -------------------------------------------- |
| `justify-content` | Spaces items along the **main axis**  | Spaces **columns** horizontally              |
| `align-items`     | Aligns items along the **cross axis** | Aligns items **vertically** within their row |

### Justify Content (Horizontal Spacing)

In Grid, `justify-content` controls how the **columns** are spaced horizontally within the grid container. This is useful when your columns don't take up the full width of the container.

```css
.grid-container {
  display: grid;
  grid-template-columns: 100px 100px 100px;
  justify-content: space-between; /* or: start, end, center, space-around, space-evenly */
}
```

Play around with this interactive demo on [Josh Comeau's blog](https://www.joshwcomeau.com/css/interactive-guide-to-grid/#alignment).

![A screenshot from Josh Comeau's blog on CSS Grid showing the justify content property](../.gitbook/assets/grid-justify-content.png)

### Align Items (Vertical Alignment)

In Grid, `align-items` controls how items are aligned **vertically** within their row. By default, grid items stretch to fill the full height of their row. You can change this:

```css
.grid-container {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  align-items: center; /* or: start, end, stretch (default) */
}
```

This is particularly useful when your grid items have different heights and you want them aligned at the top, center, or bottom of each row.

### Centering Items in a Grid

Just like with Flexbox, you can center items both horizontally and vertically by combining these properties:

```css
.grid-container {
  display: grid;
  justify-content: center;
  align-items: center;
}
```

{% hint style="info" %}
For a deeper dive into Grid alignment, check out [Josh Comeau's interactive guide to CSS Grid](https://www.joshwcomeau.com/css/interactive-guide-to-grid/#alignment-9).
{% endhint %}

## Going Further: Spanning Rows and Columns

{% hint style="warning" %}
This section covers advanced Grid features. Feel free to skip this for now and come back to it later!
{% endhint %}

In a grid, grid items can span multiple rows and/or columns to create more complex layouts like this:

![A grid with 2 columns and 4 rows. An element on the first row spans both columns. An element on the second row spans only the left column. An element on the third row also only spans the left column. A single element in the right column spans rows 2 and 3. Finally an element in the fourth row spans both columns.](../.gitbook/assets/grid-span.png)

To achieve this, we use **grid tracks**—numbered lines that indicate the start and end of each column and row. Grid tracks start at `1`.

![A grid showing the numbered grid tracks](../.gitbook/assets/grid-span-with-tracks.png)

We can position items using `grid-row` and `grid-column` with the syntax `start-track / end-track`:

```css
/* This item spans from column track 1 to column track 3 (two columns) */
.grid-item:nth-child(1) {
  grid-column: 1 / 3;
}

/* This item spans from row track 2 to row track 4 (two rows) */
.grid-item:nth-child(4) {
  grid-row: 2 / 4;
}
```

This is an advanced technique used for magazine-style layouts and complex photo galleries.
