#### UPDATE
I added the presentation role to each `li` tags in second pattern and that seemed to fix most of the issues that I encountered before.

## Background
For this experiment, I am trying to come up with an accessible tabs list solution. I tried the following two patterns

```
<div class="tabs-list" role="tablist" aria-label="Tabs List">
  <button class="tab" role="tab">Tab 1</button>
  <button class="tab" role="tab">Tab 2</button>
  <button class="tab" role="tab">Tab 3</button>
</div>
```

and

```
<ul class="tabs-list" role="tablist" aria-label="Tabs List">
  <li role="presentation>
    <button class="tab" role="tab">Tab 1</button>
  </li>
  <li role="presentation">
    <button class="tab" role="tab">Tab 2</button>
  </li>
  <li role="presentation">
    <button class="tab" role="tab">Tab 3</button>
  </li>
</ul>
```

and here is the simple JavaScript (yes, it is in es5! :P)
```
var tabsList = document.querySelector('.tabs-list');
var tabs = tabsList.querySelectorAll('.tab');
var activeTab = null;

tabs.forEach(function (tab) {
  tab.addEventListener('click', function () {
    if (!activeTab || (activeTab && activeTab !== tab)) {
      if (activeTab) {
        activeTab.removeAttribute('aria-selected');
      }
      tab.setAttribute('aria-selected', true);
      activeTab = tab;
      console.error('TAB SELECTED = ', tab.innerHTML);
    } else if (activeTab === tab) {
      // Do nothing
    }
  });
});
```

What I expected from both of these patterns:
- The screen reader should announce the index of that tab within the tabs list and the total tabs
- The screen reader should announce that the tab is selected if the focus is on the currently selected tab

I conducted tests on the following combinations:
- Chrome + VO (Mac OS)
- Chrome + ChromeVox (Mac OS)
- Safari + VO (Mac OS)

Here are the behaviors of each combinations that I observed:
- Safari + VO (Mac)
  - VO will announce the number of total tabs and the index of focused tab
  - VO announce tab as selected if we focus on selected tab
- Chrome + VO (Mac)
  - VO will announce the number of total tabs and the index of focused tab
  - VO announce every focused tab as selected
- Chrome + ChromeVox (Mac)
  - Everything works for ChromeVox
  
- Safari + VO (Mac)
  - ~~VO will NOT announce the number of total tabs and the index of focused tab~~
  - VO announce tab as selected if we focus on selected tab
- Chrome + VO (Mac)
  - ~~VO will announce the number of total tabs and the index of focused tab 
     incorrectly. It will always say tab 1 of 1 for every focused tab~~
  - VO announce every focused tab as selected
- Chrome + ChromeVox
  - ~~On ChromeVox, clicking on tab doesn't event make it selected.~~
    
These are quite surprising results for me.
- Somehow, using the `ul/li` actually make the screen reader's interpration worse. However, adding presentation role to the `li` tag seems to fix all them.
- ~~On both Safari and Chrome, VO does not produce correct number of total tabs (BAD!)~~
- On Chrome, VO announces every focused tab as `selected`. This is not good but my guess is this is due to the incompatibility between VO and Chrome.
- ~~On ChromeVox, clicking on tab doesn't even work (YIPES!)~~

So, in conclusion, both patterns seem to work just fine. However, the second pattern will require adding presentation role to each `li` tags
