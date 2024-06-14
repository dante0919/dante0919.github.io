+++
title = 'How do I fix the issue of a white blank screen showing before MainWindow is loaded'
date = 2024-06-14T15:21:30+08:00
draft = false
+++
# How do I fix the issue of a white blank screen showing before MainWindow is loaded?

I have encountered an issue where our MainWindow displays a completely white screen for 1 to 2 seconds before all components are rendered. Our clients find this white screen unacceptable, so I was tasked with fixing this issue.
  I googled many keywords and only got one effective scenario:
  
  ```c#
  WindowStyle =None, 
  AllowsTransparency =true,
  Background = new SolidColorBrush(Colors.Transparent)
   ```
  However applying this scenario requires us to re-implements the Window's maxium,minium,restore,drag,resize functions and may cause some other performance issues. After many attempts, i found a more effective way. I created and displayed a new empty NoneStyle window inside the MainWindow's Constructor (Note that the empty window must shown upon the MainWindow).When the ContentRendered event fired, release the empty window.Here is my code:
  ```c#
   public class MainWindow :Window
   {
    ///The empty NoneStyle Window
     private Window _escapeWhiteScreen;
     public MainWindow()
     {
        _escapeWhiteScreen = new Window()
        {
            WindowStyle =WindowStyle.None, 
            AllowsTransparency =true,
            Background = new SolidColorBrush(Colors.Transparent)
        };
        _escapeWhiteScreen.Show();//shown upon the MainWindow
        this.ContentRendered+=MainWindow_ContentRendered;
     }
     private void MainWindow_ContentRendered(object sender, EventArgs e)
     {
        this.ContentRendered -=MainWindow_ContentRendered;
        _escapeWhiteScreen?.Close();
        _escapeWhiteScreen = null;
     }
   }
   ``` 