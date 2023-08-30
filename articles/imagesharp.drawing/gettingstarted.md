# Getting Started

>[!NOTE]
>The official guide assumes intermediate level knowledge of C# and .NET. If you are totally new to .NET development, it might not be the best idea to jump right into a framework as your first step - grasp the basics then come back. Prior experience with other languages and frameworks helps, but is not required.

### ImageSharp.Drawing - Paths and Polygons

ImageSharp.Drawing provides several classes for building and manipulating various shapes and paths.

- @"SixLabors.ImageSharp.Drawing.IPath" Root interface defining a path/polygon and the type that the rasterizer uses to generate pixel output.
- This `SixLabors.ImageSharp.Drawing` namespace contains a variety of available polygons to speed up your drawing process.

In addition to the vector manipulation APIs the library also contains rasterization APIs that can convert your @"SixLabors.ImageSharp.Drawing.IPath"s to pixels.

### Drawing Polygons

ImageSharp provides several options for drawing polygons whether you want to draw outlines or fill shapes.

#### Minimal Example

```c#
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Processing;
using SixLabors.ImageSharp.Drawing.Processing;

Image image = ...; // create any way you like.

IPath yourPolygon = new Star(x: 100.0f, y: 100.0f, prongs: 5, innerRadii: 20.0f, outerRadii:30.0f);

image.Mutate( x=> x.Fill(Color.Red, yourPolygon)); // fill the star with red

```

#### Expanded Example

```c#
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Processing;
using SixLabors.ImageSharp.Drawing.Processing;
using SixLabors.ImageSharp.PixelFormats;

Image image = ...; // Create any way you like.

// The options are optional
DrawingOptions options = new()
{
    GraphicsOptions = new()
    {
        ColorBlendingMode  = PixelColorBlendingMode.Multiply
    }
};

Brush brush = Brushes.Horizontal(Color.Red, Color.Blue);
Pen pen = Pens.DashDot(Color.Green, 5);
IPath yourPolygon = new Star(x: 100.0f, y: 100.0f, prongs: 5, innerRadii: 20.0f, outerRadii:30.0f);

// Draws a star with horizontal red and blue hatching with a dash-dot pattern outline.
image.Mutate(x=> x.Fill(options, brush, yourPolygon)
                   .Draw(option, pen, yourPolygon));
```

### API Cornerstones for Polygon Rasterization
Our `Fill` APIs always work off a `Brush` (some helpers create the brush for you) and will take your provided set of paths and polygons filling in all the pixels inside the vector with the color the brush provides.

Our `Draw` APIs always work off the `Pen` where we processes your vector to create an outline with a certain pattern and fill in the outline with an internal brush inside the pen.


### Drawing Text

ImageSharp.Drawing provides several options for drawing text all overloads of a single `DrawText` API. Our text drawing infrastructure is build on top of our [Fonts](../fonts/index.md) library. (See [SixLabors.Fonts](../fonts/index.md) for details on handling fonts.)

#### Minimal Example

```c#
using SixLabors.Fonts;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Processing;
using SixLabors.ImageSharp.Drawing.Processing;

Image image = ...; // Create any way you like.
Font font = ...; // See our Fonts library for best practices on retrieving one of these.
string yourText = "this is some sample text";

image.Mutate(x=> x.DrawText(yourText, font, Color.Black, new PointF(10, 10)));
```

#### Expanded Example

```c#
using SixLabors.Fonts;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Processing;
using SixLabors.ImageSharp.Drawing.Processing;
using SixLabors.ImageSharp.PixelFormats;

Image image = ...; // Create any way you like.
Font font = ...; // See our Fonts library for best practices on retrieving one of these.

// The options are optional
RichTextOptions options = new(font)
{
    Origin = new PointF(100, 100), // Set the rendering origin.
    TabWidth = 8, // A tab renders as 8 spaces wide
    WrappingLength = 100, // Greater than zero so we will word wrap at 100 pixels wide
    HorizontalAlignment = HorizontalAlignment.Right // Right align
};

var brush = Brushes.Horizontal(Color.Red, Color.Blue);
var pen = Pens.DashDot(Color.Green, 5);
string text = "sample text";

// Draws the text with horizontal red and blue hatching with a dash-dot pattern outline.
image.Mutate(x=> x.DrawText(options, text, brush, pen));
```
