# Drop Down Mimic - AutoHotkey - Temporary Repo

This is a "Quick & Dirty" script example of functions and the method I use with AutoHotkey to mimic the "Drop Down" behavior of Linux terminals like '**Guake**', '**Tilda**', and '**Yakuake**' for 'Windows' windows/guis. This example is set for use with the regular 'cmd.exe' executable, but you can use whatever executable you like as long as you know the path and know the window Title, Class, or other identifier as per WinGet().

## Disclaimer

As stated above this is a temporary repository as I have plans for a more thorough and clean script.

***This will eventually have "Drop-Down" animation.***

- [Drop Down Mimic - AutoHotkey - Temporary Repo](#drop-down-mimic---autohotkey---temporary-repo)
  - [Disclaimer](#disclaimer)
  - [Files](#files)
  - [Example Script Display](#example-script-display)

## Files

|File|Description|
|---:|:---|
|[drop_down_example.ahk](src/scripts/drop_down_example.ahk)|Example file with e every function, hotkey, and sub needed for the example.|

## Example Script Display

```AutoHotkey
; ╓───────────────────────────────────────────────────────────────────╖
; ║ ☍ This is a "Quick & Dirty" script example of functions and the   ║
; ║ ☍ method I use with AutoHotkey to mimic the "Drop Down" behavior  ║
; ║ ☍ of Linux terminals like 'Guake', 'Tilda', and 'Yakuake' for     ║
; ║ ☍ 'Windows' windows/guis. This example is set for use with the    ║
; ║ ☍ regular 'cmd.exe' executable, but you can use whatever          ║
; ║ ☍ executable you like as long as you know the path and know       ║
; ║ ☍ the window Title, Class, or other identifier as per WinGet().   ║
; ╙───────────────────────────────────────────────────────────────────╜

; ╓───────────────────────────────────────────────────────────────────────╖
; ║ ☍ Disclaimer- As per "Quick & Dirty" I have tried my best to comment  ║
; ║ ☍ this script, but I have plans to do more with this so please excuse ║
; ║ ☍ this if it's not 100% perfect.                                      ║
; ╙───────────────────────────────────────────────────────────────────────╜

; ╓────────────────────────────────────────────────────────────────────────╖
; ║ ☍ This will eventually have the actual "DropDown" animation. I already ║
; ║ ☍ have the system built to do so, but I need to incorporate it here.   ║
; ╙────────────────────────────────────────────────────────────────────────╜

; ╓──────────────────────────────────────────╖
; ║ ☍ Performance and other AHK directives ☍ ║
; ╙──────────────────────────────────────────╜
#SingleInstance, Force
#KeyHistory, 0
SetBatchLines, -1
ListLines, Off
SetWorkingDir, %A_ScriptDir%
SetWinDelay, 0
#Persistent

; ╓────────────────────────────────────────────────────────────────────╖
; ║ ☍ If running as Administrator is necessary then you can un-comment ║
; ║ ☍ this next section to check and RunAsAdmin internally.            ║
; ╙────────────────────────────────────────────────────────────────────╜
; if (! A_IsAdmin)
; {
;     run,*RunAs %A_ScriptFullPath%
;     ExitApp
; }
; return
; ╓─────────────╖
; ║ ☍ Hotkeys ☍ ║
; ╙─────────────╜
; ╓──────────────────────────────────────────────────────────────╖
; ║ ☍ This Hotkey checks if "ahk_class ConsoleWindowClass" (CMD) ║
; ║ ☍ exists and if it doesn't it runs it by real PATH or %PATH% ║
; ║ ☍ and if it already exists it checks if it's active and      ║
; ║ ☍ starts a timer if it's active and shuts the timer off if   ║
; ║ ☍ it's not active.                                           ║
; ╙──────────────────────────────────────────────────────────────╜ 
^`:: ; Ctrl+` to toggle the window and timer.
    ; COMSPEC is another PATH for cmd.exe
    ; Change COMSPEC with your path of Terminal executable
    ; and provide whatever window Title, Class, or other
    ; AutoHotkey window identifier to check for.  
	RunToggle(COMSPEC,"ahk_class ConsoleWindowClass")
    ; Delay to give time for window to load.
	sleep,200
    ; Check if Window is active
	if WinActive("ahk_class ConsoleWindowClass")
    {
        SetTimer,Attach_CMD,-1 ; set CMD watching On
    }
	else
    {
        SetTimer,Attach_CMD,Off ; set CMD watching Off
    }
return

; ╓───────────────╖
; ║ ☍ Functions ☍ ║
; ╙───────────────╜

; ╓─────────────────────────────────────────────────────────────╖
; ║ ☍ attachWinToUpperDesktopBySub                              ║
; ║ ☍ This is a sister/wrapper function to 'Attach_Window'      ║
; ║ ☍ specifically for attaching a window to the top of         ║
; ║ ☍ the Desktop; this sort of mimics drop-down functionality. ║
; ║ ☍ I can actually do the 'Drop-Down' animation, but I need   ║
; ║ ☍ to set aside time to do so.                               ║
; ║ ☍                                                           ║
; ║ ☍ Params:                                                   ║
; ║ ☍      sub:    The 'Sub' to attach this function to         ║
; ║ ☍              This allows this function to be attached     ║
; ║ ☍              by 'toggle' or 'timer'.                      ║
; ║ ☍      win*:   1 to 4 window params to be evaluated by      ║
; ║ ☍              WinExist() in normal AutoHotkey fashion.     ║
; ║ ☍              "A" by itself woud work on any active window ║
; ╙─────────────────────────────────────────────────────────────╜
attachWinToUpperDesktopBySub(sub,win*)
{
	if (id := WinExist(win[1],win[2],win[3],win[4]))
    {
		if (! WinExist("ahk_id " id))
        {
			SetTimer,%sub%,Off
			return
		}
		if ! WinActive("ahk_id " id)
        {
			WinMinimize,% "ahk_id " id
			return
	    }
        ; Replace this if statement if want faster resize, but
        ; it will be a bit glitchy.
        ; if (WinActive("ahk_id " id))        
		if (WinActive("ahk_id " id) And ! GetKeyState("LButton","P"))
        {
			wid := WinExist("Program Manager")
			aid := WinExist("ahk_id " id)
			WinGetPos,wx,wy,ww,wh,% "ahk_id " id
			if (Round(wy)!=32) Or (Round(wx)!=Round((A_ScreenWidth/2)-(ww/2)))
            {
				WinRestore,% "ahk_id " id
                ; Switch these next two lines as an alternate
                ; to hide the Title bar of the child window
                ; as an alternative to remove a windows winow
                ; decorations. 32px is the average height.
                ; 
                ; Attach_Window(wid,aid,"Top","Inner",,,-32)
                Attach_Window(wid,aid,"Top","Inner")
			}
			wid := aid := ww := wh := wx := wy := ""
		}
		SetTimer,%sub%,-1
	}
}

; ╓─────────────────────────────────────────────────────╖
; ║ ☍ This is the main worker function of this example. ║
; ╙─────────────────────────────────────────────────────╜
; ╓─────────────────────────────────────────────────────────────────────────────╖
; ║ ☍ Attach_Window - Attach a child window to a parent window                  ║
; ║ ☍ Params:                                                                   ║
; ║ ☍      parent: The parent id (HWND).                                        ║
; ║ ☍      child: The parent id (HWND).                                         ║
; ║ ☍      side: Which side of parent to attach child to.                       ║
; ║ ☍      inout: Inner or outer side of parent to attach child to.             ║
; ║ ☍      resize: Resize child to match (parent size - remaining window size). ║
; ║ ☍      xAdjust: X position Offset of child window.                          ║
; ║ ☍      yAdjust: Y position Offset of child window.                          ║
; ║ ☍ I plan to re-write this with better param handling                        ║
; ╙─────────────────────────────────────────────────────────────────────────────╜
Attach_Window(parent,child,side:="Bottom",inout:="Outer",resize:=false,xAdjust:=0,yAdjust:=0)
{	
    if (WinExist("ahk_id " parent) And WinExist("ahk_id " child))
    {	
        side_list:=["Bottom","Left","Top","Right"]
        for sideidx, sides in %side_list%
        {
            if InStr(side,sides)
            {
                enabled:=true
            }
            if ! enabled
            {
                return
            }
            if inout not in Outer,Inner
            {
                return
            }
        }
        WinGetPos,px,py,pw,ph,ahk_id %parent%
        WinGetPos,cx,cy,cw,ch,ahk_id %child%
        xMargin:=!resize?(pw-cw)/2:0
        yMargin:=!resize?(ph-ch)/2:0
        destW:=resize?pw:cw,destH:=resize?ph:ch
        destX	:=	InStr(side,"Left")?((inout="Outer")?px-destW:px)
        :	InStr(side,"Right")?((inout="Outer")?px+pw:px+(xMargin*2))
        :	px+xMargin
        destY	:=	InStr(side,"Top")?((inout="Outer")?py-destH:py)
        :	InStr(side,"Bottom")?((inout="Outer")?py+ph:py+(yMargin*2))
        :	py+yMargin
        if (WinGet("MinMax","ahk_id " parent)<1)
        {
            if (cx!=destX Or cy!=destY Or cw!=destW Or ch!=destH)
            {
                WinMove,ahk_id %child%,,destX+xAdjust,destY+yAdjust,destW,destH
            }
        }
    }
}

; ╓─────────────────────────────────────────────────────────────╖
; ║ ☍ RunToggle - Run or Toggle a window                        ║
; ║ ☍      Run an executable if the window doesn't exist        ║
; ║ ☍      or Minimize or Restore the window if it does.        ║
; ║ ☍ Params:                                                   ║
; ║ ☍      path:   Either the full path or the                  ║
; ║ ☍              executable name if in %PATH%.                ║
; ║ ☍      win*:   1 to 4 window params to be evaluated by      ║
; ║ ☍              WinExist() in normal AutoHotkey fashion.     ║
; ║ ☍              "A" by itself woud work on any active window ║
; ╙─────────────────────────────────────────────────────────────╜
RunToggle(path,win*)
{
	if ! (id := WinExist(win[1],win[2],win[3],win[4]))
    {
		run,%path%,,UseErrorLevel,pid
		if ErrorLevel
        {
            ; Un-comment for debugging purposes
			; MsgBox,% (32+496),%path% Error,Could not run %path% for an unknown reason
			return
		}
	}
    else
    {
		WinGet,pid,PID,% "ahk_id " id
		WinGet,mm,MinMax,% "ahk_id " id
		if (mm != -1)
        {
			WinMinimize
		}
        else 
        {
			WinRestore
		}
		return {"pid":pid,"id":WinExist("ahk_pid " pid)}
	}
}

WinGet(attrib,win*)
{	
    WinGet,result,%attrib%	,	% win[1]
    ,	% win[2]
    ,	% win[3]
    ,	% win[4]
    if (SubStr(attrib,-3)="List")
    {	
        tmp:=[]
        if (attrib="List")
            loop,%result%
            tmp[A_Index]:=result%A_Index%
        else
            loop,parse,result,`n
            tmp[A_Index]:=A_LoopField
        result:=tmp
    }
    return result
}

; ╓──────────╖
; ║ ☍ Subs ☍ ║
; ╙──────────╜
Attach_CMD:
    attachWinToUpperDesktopBySub(A_ThisLabel,"ahk_class ConsoleWindowClass")
return
```
