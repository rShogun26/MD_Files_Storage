# ⚙️ Automation Scripts & Macros: MS Office & Google Workspace

A centralized reference guide containing automation scripts and macros for Microsoft Word (VBA) and Google Docs (Google Apps Script).

---

## 1. Microsoft Word (VBA Macros)

### 1.1 Stack Images Vertically (Full Page Width)
Takes every selected image (`Ctrl + click`), stretches it to match the exact page width, and stacks them vertically with 0 distance and zero overlapping.

```vba
Sub StackFullPageWidth()
    Dim shp As Shape
    Dim nextTop As Single
    Dim isFirst As Boolean
    
    isFirst = True
    
    For Each shp In Selection.ShapeRange
        ' 1. Unlock image text wrapping restrictions
        shp.WrapFormat.Type = wdWrapFront
        
        ' 2. Detach position from margins and link directly to page edges
        shp.RelativeHorizontalPosition = wdRelativeHorizontalPositionPage
        shp.RelativeVerticalPosition = wdRelativeVerticalPositionPage
        
        ' 3. Stretch across entire page width (612 for US Letter. Change to 595 if using A4)
        shp.LockAspectRatio = msoTrue
        shp.Width = 612 
        
        ' 4. Align perfectly to the left paper edge
        shp.Left = 0
        
        ' 5. Handle vertical positioning with 0 distance overlapping
        If isFirst Then
            ' Keep first picture where it is vertically, but remember its bottom edge
            nextTop = shp.Top + shp.Height
            isFirst = False
        Else
            ' Snap this picture's top directly to the previous picture's bottom
            shp.Top = nextTop
            ' Calculate the new bottom boundary for the next picture
            nextTop = shp.Top + shp.Height
        End If
    Next shp
End Sub
```

> **Page Width Reference:** Set to `612` (US Letter standard). If your document layout is **A4**, change `shp.Width = 612` to `shp.Width = 595`.

---

### 1.2 Batch Convert Inline Images to Floating
Converts all **In Line with Text** images across the document into floating **In Front of Text** (`wdWrapFront`) shapes.

```vba
Sub ConvertInlineToInFrontOfText()
    Dim i As Long
    Dim doc As Document
    Dim ils As InlineShape
    Dim shp As Shape
    
    Set doc = ActiveDocument
    
    ' Optimize performance and turn off screen flickering
    Application.ScreenUpdating = False
    
    ' Loop backwards through inline shapes to avoid skipping items during conversion
    For i = doc.InlineShapes.Count To 1 Step -1
        Set ils = doc.InlineShapes(i)
        
        ' Ensure the item is an image/picture
        If ils.Type = wdInlineShapePicture Or ils.Type = wdInlineShapeLinkedPicture Then
            ' Convert inline image to a floating shape
            Set shp = ils.ConvertToShape
            
            ' Set the wrapping style to In Front of Text
            shp.WrapFormat.Type = wdWrapFront
        End If
    Next i
    
    Application.ScreenUpdating = True
    MsgBox "All inline images have been successfully converted to floating (In Front of Text wrapping).", vbInformation, "Done!"
End Sub
```

---

### ⚡ How to Run VBA Macros in Microsoft Word
1. Press `Alt + F11` to open the VBA Editor.
2. Click **Insert** $\rightarrow$ **Module**.
3. Paste the code into the module window and close the VBA Editor.
4. If using `StackFullPageWidth`, hold `Ctrl` and select your pictures in the exact order you want them stacked (top to bottom).
5. Press `Alt + F8`, select the macro name, and click **Run**.

---

## 2. Google Docs (Google Apps Script)

### 2.1 Stack Images Vertically (Full Page Width & Zero Gap)
Iterates through all images in the document, calculates proportional height to maintain aspect ratio, expands width to full page dimensions, and collapses paragraph spacing so the images stack edge-to-edge with zero gap.

```javascript
function stackImagesFullPageWidth() {
  var doc = DocumentApp.getActiveDocument();
  var body = doc.getBody();
  
  // Page width in points (612 for US Letter, 595 for A4)
  var targetWidth = body.getPageWidth();
  
  // Clear page margins for seamless border-to-border stacking
  body.setMarginLeft(0);
  body.setMarginRight(0);
  body.setMarginTop(0);
  body.setMarginBottom(0);

  var paragraphs = body.getParagraphs();
  
  for (var i = 0; i < paragraphs.length; i++) {
    var p = paragraphs[i];
    var childCount = p.getNumChildren();
    
    for (var j = 0; j < childCount; j++) {
      var child = p.getChild(j);
      if (child.getType() === DocumentApp.ElementType.INLINE_IMAGE) {
        var img = child.asInlineImage();
        
        // Preserve aspect ratio
        var origWidth = img.getWidth();
        var origHeight = img.getHeight();
        var newHeight = (targetWidth / origWidth) * origHeight;
        
        img.setWidth(targetWidth);
        img.setHeight(newHeight);
        
        // Zero out paragraph spacing to stack with 0 distance
        p.setSpacingBefore(0);
        p.setSpacingAfter(0);
        p.setLineSpacing(1);
      }
    }
  }
}
```

---

### 2.2 Batch Convert Inline Images to Floating ("Above Text")
Converts all embedded inline images in the document into floating `PositionedImage` elements anchored to the text with `ABOVE_TEXT` layout (Google Docs equivalent of Word's "In Front of Text").

```javascript
function convertInlineToFloating() {
  var doc = DocumentApp.getActiveDocument();
  var body = doc.getBody();
  var paragraphs = body.getParagraphs();
  var count = 0;

  for (var i = 0; i < paragraphs.length; i++) {
    var p = paragraphs[i];
    
    // Loop backwards to safely remove inline elements
    for (var j = p.getNumChildren() - 1; j >= 0; j--) {
      var child = p.getChild(j);
      if (child.getType() === DocumentApp.ElementType.INLINE_IMAGE) {
        var inlineImg = child.asInlineImage();
        var blob = inlineImg.getBlob();
        var w = inlineImg.getWidth();
        var h = inlineImg.getHeight();

        // Create floating PositionedImage anchored to paragraph
        var posImg = p.addPositionedImage(blob);
        posImg.setLayout(DocumentApp.PositionedLayout.ABOVE_TEXT)
              .setWidth(w)
              .setHeight(h)
              .setLeftOffset(0)
              .setTopOffset(0);

        // Remove original inline image
        inlineImg.removeFromParent();
        count++;
      }
    }
  }

  DocumentApp.getUi().alert("Conversion Complete", count + " inline images converted to floating (Above Text).", DocumentApp.getUi().ButtonSet.OK);
}
```

---

### 2.3 Auto-Format Numbered Questions & "Output:" Labels
Scans the document to detect lines beginning with numbers (e.g., `1. `, `2. `) and instances of `Output:`, then bolds, underlines, and strips background highlight colors.

```javascript
function formatQuestionsAndOutputs() {
  var body = DocumentApp.getActiveDocument().getBody();

  // 1. Bold and Underline Question Titles (and remove highlight)
  var paragraphs = body.getParagraphs();
  for (var i = 0; i < paragraphs.length; i++) {
    var text = paragraphs[i].getText().trim();
    // Checks if the line starts with a number followed by a dot (e.g., "1. ", "2. ")
    if (/^\d+\.\s/.test(text)) {
      paragraphs[i].setBold(true);
      paragraphs[i].setUnderline(true);
      paragraphs[i].editAsText().setBackgroundColor(null); // Removes any color
    }
  }

  // 2. Bold and Underline every "Output:" (and remove highlight)
  var search = body.findText("Output:");
  while (search) {
    var textElement = search.getElement().asText();
    var start = search.getStartOffset();
    var end = search.getEndOffsetInclusive();

    textElement.setBold(start, end, true);
    textElement.setUnderline(start, end, true);
    textElement.setBackgroundColor(start, end, null); // Removes any color
    
    search = body.findText("Output:", search);
  }
}
```

---

### ⚡ How to Run Apps Script in Google Docs
1. In your Google Doc, navigate to **Extensions** $\rightarrow$ **Apps Script**.
2. Replace any placeholder code with the desired script above.
3. Click the **Save** icon (`Ctrl + S`).
4. Select the function you want to execute from the dropdown at the top and click **Run**.
5. When running for the first time, click **Review Permissions** $\rightarrow$ select your Google account $\rightarrow$ **Advanced** $\rightarrow$ **Go to Untitled project (unsafe)** $\rightarrow$ **Allow**.
```