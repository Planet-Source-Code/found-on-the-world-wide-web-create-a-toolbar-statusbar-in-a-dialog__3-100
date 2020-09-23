<div align="center">

## Create a toolbar/statusbar in a dialog


</div>

### Description

Create a toolbar/statusbar in a dialog.

To add a control bar to a dialog, you must create the control bar as usual, and then make room for the control bar within the client area of the dialog. For the control bar to function properly, the dialog must duplicate some of the functionality of frame windows. If you want ON_UPDATE_COMMAND_UI handlers to work for the control bars, you also need to derive new control bar classes, and handle the WM_IDLEUPDATECMDUI message. If your dialog is not the main window of your application, you will also need to modify its parent frame window to pass the WM_IDLEUPDATECMDUI message on to the dialog's control bars.

To make room for a control bar within the client area of the dialog, follow these steps in your dialog's OnInitDialog() function:

http://stingsoft.com/mfc_faq/chapter6/chapter6_5.htm
 
### More Info
 


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[Found on the World Wide Web](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/found-on-the-world-wide-web.md)
**Level**          |Unknown
**User Rating**    |5.0 (10 globes from 2 users)
**Compatibility**  |C\+\+ \(general\)
**Category**       |[Controls/ Forms/ Dialogs/ Menus](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/controls-forms-dialogs-menus__3-3.md)
**World**          |[C / C\+\+](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/c-c.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/found-on-the-world-wide-web-create-a-toolbar-statusbar-in-a-dialog__3-100/archive/master.zip)





### Source Code

```
To make room for a control bar within the client area of the dialog, follow these steps in your dialog's OnInitDialog() function:
Create the control bars.
CRect rcClientStart;
CRect rcClientNow;
GetClientRect(rcClientStart);
RepositionBars(AFX_IDW_CONTROLBAR_FIRST,
AFX_IDW_CONTROLBAR_LAST,0, reposQuery,rcClientNow);
Figure out how much room the control bars will take by using the reposQuery option of RepositionBars():
CPoint ptOffset(rcClientStart.left - rcClientNow.left,
        rcClientStart.top - rcClientNow.top);
ptOffset.y += ::GetSystemMetrics(SM_CYMENU);
CRect rcChild;
CWnd* pwndChild = GetWindow(GW_CHILD);
while (pwndChild)
{
  pwndChild->GetWindowRect(rcChild);
  rcChild.OffsetRect(ptOffset);
  pwndChild->MoveWindow(rcChild, FALSE);
  pwndChild = pwndChild->GetNextWindow();
}
Move all the controls in your dialog to account for space used by control bars at the top or left of the client area. If your dialog contains a menu, you also need to account for the space used by the menu:4. Increase the dialog window dimensions by the amount of space used by the control bars:
CRect rcWindow;
GetWindowRect(rcWindow);
rcWindow.right += rcClientStart.Width()
         - rcClientNow.Width();
rcWindow.bottom += rcClientStart.Height()
          - rcClientNow.Height();
MoveWindow(rcWindow, FALSE);
Position the control bars using RepositionBars().
To update the first pane of a status bar with menu item text, you must handle WM_MENUSELECT, WM_ENTERIDLE, and WM_SETMESSAGESTRING in your dialog class. You need to duplicate the functionality of the CFrameWnd handlers for these messages. See the CModelessMain class in the sample program for examples of these message handlers.
To allow ON_UPDATE_COMMAND_UI handlers to work for other status bar panes and for toolbar buttons, you must derive new control bar classes and implement a message handler for WM_IDLEUPDATECMDUI. This is necessary because the default control bar implementations of OnUpdateCmdUI() assume the parent window is a frame window. However, it doesn't do anything but pass the parent window pointer on to a function which only requires a CCmdTarget pointer. Therefore, you can temporarily tell OnUpdateCmdUI() that the parent window pointer you are giving it is a CFrameWnd pointer to meet the compiler requirements. Here's an example:
LRESULT CDlgToolBar::OnIdleUpdateCmdUI(WPARAM wParam,LPARAM lParam)
{
  if (IsWindowVisible())
  {
    CFrameWnd* pParent = (CFrameWnd*)GetParent();
    if (pParent)
    OnUpdateCmdUI(pParent, (BOOL)wParam);
  }
  return 0L;
}
To pass WM_IDLEUPDATECMDUI messages on to dialogs other than the main window, save dialog pointers in your frame window class and create a WM_IDLEUPDATECMDUI handler in that class. The handler should send the WM_IDLEUPDATECMDUI message on to the dialog child windows by using CWnd::SendMessageToDescendants(). Then perform default processing for the message within the frame window.
```

