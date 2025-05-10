# Extended List Properties
The class supports extended list control properties, like word wrap, unicode, hot tracking. It also allows to customize the entire list, the rows, the columns, or the individual cells. It works in C6.3 and newer Clarion versions.  
  
It supports both hand coded lists and standard browse boxes (ABC and Clarion template chains). All you need is to declare a class instance and set up initialization options:
```
!- Employees list
lstEmployees                  CLASS(TListboxExtProps)
Init                            PROCEDURE(SIGNED pListFeq, QUEUE pFromQ), DERIVED
                              END

  CODE
  OPEN(Window)
  lstEmployees.Init(?lstEmployees, employees)

lstEmployees.Init             PROCEDURE(SIGNED pListFeq, QUEUE pFromQ)
  CODE
  PARENT.Init(pListFeq, pFromQ)
  
  !- make rows a bit higher
  SELF.FEQ{PROP:LineHeight} = 24

  !- overwrite column behavior
  SELF.EnableWordWrap(2, TRUE)    !- name column
  SELF.EnableWordWrap(3, TRUE, 6) !- address column
  SELF.EnableWordWrap(4, TRUE)    !- post column

```
  
Here is a short video:
https://github.com/mikeduglas/Extended-List-Properties/blob/master/video/extlistprops.avi?raw=true  

### Word wrap property
Automatically wrap text in a cell to the next line when it reaches the end of a line or a specified margin.  
![Word wrap property](https://github.com/user-attachments/assets/d3f06911-7a47-4a6b-a9b1-29e2621bfd51)

### Word wrap + reduced font size
If a text does not fit into a cell even with the word wrap option enabled, the font size will be reduced to fit the text in the cell.  
![Word wrap + reduced font size](https://github.com/user-attachments/assets/0f7d744e-7f37-4b92-9f0c-91341c0afa0e)

### Unicode property
Enables unicode (UTF8 and UTF16).  
![Unicode](https://github.com/user-attachments/assets/e40f8555-30d7-4cad-a21c-b1bea74f1819)
```
lstLanguages.Init             PROCEDURE(SIGNED pListFeq, QUEUE pFromQ)
  CODE
  PARENT.Init(pListFeq, pFromQ)
  
  !- make rows a bit higher
  SELF.FEQ{PROP:LineHeight} = 24
  
  !- enable word wrap and unicode (UTF8) options in "Phrase" column
  SELF.EnableWordWrap(2, TRUE)    !- Phrase column
  SELF.EnableUnicode(2, CP_UTF8)  !- Phrase column
```

### Hot tracking
List rows can be hot tracked when end-users hover over them.  
```
  lstEmployees.EnableHotRow(Color:DarkGray)                 !- dark gray background while hot tracking.
  lstSongs.EnableHotRow(COLOR:NONE, COLOR:Blue, FONT:bold)  !- blue bold text while hot tracking.
```

### Custom drawing: bar chart
Display numeric column values as bars.  
![bar chart](https://github.com/user-attachments/assets/c9b65a42-b2bc-430f-9bc9-5bcea196f9b4)
```
lstStudents.BeforeDrawCell    PROCEDURE(LONG pColumn, LONG pRow, LONG pBackColor, LONG pTextColor, | 
                                      *TLogicalFont pFont, *STRING pText, *STRING pPicture, |
                                      *TRect pCellRect, *TRect pTextRect, *LONG pFormat, *LONG pCodePage)
  CODE
  IF pColumn = 4  !- Height
    IF bHeightAsBar
      SELF.DrawHeightBar(pRow, pCellRect)   !- draw bar chart
      RETURN FALSE                          !- FALSE means don't draw the cell text
    END
  END
  
  RETURN TRUE
```

### Custom drawing: marked rows
Mark list rows according to some criterion.  
![marked rows](https://github.com/user-attachments/assets/0d6d2c78-d2f3-4e6c-9827-4a3ded88a76c)
```
lstLanguages.AfterDrawRow     PROCEDURE(LONG pRow, TRect pRowRect)
  CODE
  IF languages.Language = 'Russian'
    SELF.MarkRow(pRowRect)
  END
```

### Custom drawing: multi row images
Display an image associated with a group of records.  
![multi row images](https://github.com/user-attachments/assets/9273b95c-a4ea-4e6b-9225-4ca9de033a91)
```
lstSongs.BeforeDrawCell       PROCEDURE(LONG pColumn, LONG pRow, LONG pBackColor, LONG pTextColor, *TLogicalFont pFont, *STRING pText, *STRING pPicture, |
                                      *TRect pCellRect, *TRect pTextRect, *LONG pFormat, *LONG pCodePage)
  CODE
  IF pColumn = 1
    CASE songs.TrackNumber 
    OF 1 TO 2
      !- Display album title and artist name in first 2 rows, just above the album image.
      pText = CHOOSE(songs.TrackNumber, songs.Album, songs.Artist)
      SELF.DrawText(pText, pPicture, pTextRect, pFormat + DT_CALCRECT, pCodePage)
      SELF.AdjustTextRect(pColumn, pCellRect, pTextRect)
      SELF.dc.SetTextColor(COLOR:Green)
      RETURN TRUE
    ELSE
      !- Display the image starting from the 3rd row.
      SELF.DrawAlbumImage(pCellRect, songs.TrackNumber-2)
      RETURN FALSE
    END
  END
  
  RETURN TRUE
```

### Requirements
Clarion 6.3 and newer, ABC/Clarion

### Contacts
mikeduglas@yandex.ru

### Price
Negotiable