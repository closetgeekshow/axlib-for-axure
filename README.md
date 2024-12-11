# Axure Extension Library
Axure's front-end component library has passed the test in RP9.x version

## Fork Description
I just ran it through Google Translate to translate all the strings, errors and comments into English

## hint
  It is recommended to use it with **JsBox**. **JsBox** is an Axure code editor component version that calls axlib. In addition to being able to program directly in Axure, it can also be externalized to VSCode for development. It is recommended to jump directly to the JsBox repository for more information:
  - https://gitee.com/wavef/jsbox (Code Cloud)
  - https://github.com/wavef/jsbox (Github)

## Components

### axlib-v3
  Core library, including many practical Axure operations and other functions

#### Loading method

- Add an *“On load - Open external link”* interaction in Axure and enter the following code:

  ```js
  $axure.utils.loadJS('https://ax.minicg.com/axlib-v3.min.js', ()=>{
    console.log(axlib);
  });
  ```

- You can also add web fonts to load, the code is as follows:

  ```html
  "><script src="https://ax.minicg.com/axlib-v3.min.js"></script><link href="
  ```

- If you want to create your own components with `axlib`, and install it using `npm` and compile it locally, you need to import it with the following code:

  ```shell
  // Installation (command line)
  npm i axlib

  // If you use `jslib` to make your component (command line)
  git clone https://gitee.com/wavef/jslib YOUR_LIBRARY_FOLDER
  cd YOUR_LIBRARY_FOLDER
  pnpm install
  pnpm add axlib
  ```

  ```js
  //Static import
  import 'axlib';
  console.log(axlib);

  // Dynamic import
  import('axlib');
  console.log(axlib);
  ```

#### Functionality Extension

- #### Repeater
   
    ```js
    /*
     * Get repeater data (real time)
     * format:
     * default - returns the original axure repeater data format
     * auto - Get data according to each row, the type is an array, each element in the array is an object, and the value of each column is marked
     * row - Get data according to each row. The type is object. The key value is the value of each row in the first column. Trim can only get the value of the first position.
     * col - Get data by each column, the type is an object, the key value is the column name (lowercase)
     * tree - returns a tree data structure. In addition, the trim parameter can remove empty values. The headers must be named C1, C2, C3...
     */

    // Get the repeater data (only the data at the time of initialization is obtained, and the data interacted with by Axure or operated by code will not be obtained)
    $axure('@Repeater name').getRepeaterDataOnce({ format: 'row', trim: true });

    // Insert repeater data (only accept axure raw data format)
    $axure('@Repeater name').addRepeaterData(
      [
        {
          'col1': { type: 'text', text: 'r1c1' },
          'col2': { type: 'text', text: 'r1c2' },
        },
        {
          'col1': { type: 'text', text: 'r2c1' },
          'col2': { type: 'text', text: 'r2c2' },
        }
      ]
    );

    // Delete n lines of repeater data
    let rows = 3;
    $axure('@Repeater name').deleteRepeaterData(rows);

    // Refresh the repeater (refresh is required to obtain real-time data after inserting, updating, deleting, and clearing data)
    // A REPEATER_UPDATED event will be emitted, which can be listened to via repeater.$().on('REPEATER_UPDATED', callback)
    $axure('@Repeater name').refreshRepeater();

    // Override repeater data (only accepts axure raw data format)
    $axure('@Repeater name').setRepeaterData(data);

    // Clear repeater data
    $axure('@Repeater name').clearRepeaterData();

    // Listen for repeater updates (also supports add, delete, etc.)
    $axure('@Repeater name').on('REPEATER_DATA_UPDATED', (e, res) => {
      console.log(res);
    });
    ```

- #### Dynamic Panel - Dynamic Panel
```js
// Get all states of dynamic panels
$axure('@dynamic panel name').getPanelStates();

// Get the current status of the dynamic panel
$axure('@dynamic panel name').getCurrentPanelState();

// The dynamic panel jumps to the state with the specified name
let name = 'State 1';
$axure('@dynamic panel name').setPanelStateByName(name);

// The dynamic panel jumps to the specified index
$axure('@dynamic panel name').setPanelStateByIndex(1);
```


- ###### Page jump
```js
// Inject method into page jump method (native interaction can also be triggered)
$axure.beforeNav((options,stop)=>{
  console.log(options); /* You can actually add transition effects here (actual test) */
  stop(true); /* Executing this statement will block the native jump*/
});

// Manual jump
$axure.navigate({
  url: 'page_1.html',
  target: 'frame',
  includeVariables: true,
  frame: $iframe.get(0)
});
```


- ##### Simplify the method of obtaining ID and node
```js
// Return the id of the first element with the same name
$axure('@element name').getId();

// Returns the first DOM node of the element with the same name
$axure('@element name').getEl();
```

- ##### Setting and getting global variables
```js
// Set global variables (you need to add the global variable name in Axure before it can take effect)
axlib.setGlobalVar('varName', 'value');

// Get the value of the global variable
axlib.getGlobalVar('varName');
```

- ##### Set the mouse pointer style
```js
// Set the element's pointer style to hand
$axure('@element name').buttonMode(true);

// Set the element to not sense any mouse events
$axure('@element name').ghostMode(true);
  ```
 
#### Additional Features

- ##### Load plugin
  
  ```js
  const { toast, swal2 } = axlib.plugins;
  yepnope({
    load: [...toast.files, ...swal2.files],
    complete: ()=>{ console.log($.toast, Swal) }
  });
  ```

- ##### Change interface layout

  ```js
  /*
   * Parameters:
   * 0 ~ default layout (keep folding)
   * 1 ~ Hide the top bar, zoom by default
   * 2 ~ Hide the top bar and scale by width
   * 3 ~ Hide the top bar and scale by height
   */
  axlib.layout(2);
  ```

- ##### Online reading and saving data (new)
  ```js
  // Initialization
  // Parameter: warehouse id, available at https://json.minicg.com
  // window.jsonbin === axlib.jsonbin
  let bin = new jsonbin('64a6db339d312622a37b3ebc');

  // Read data
  bin.read().then(res=>console.log(res));

  // Update data (data overwrite)
  bin.update({foo:'bar'});

  // Create a new warehouse (need to set API-KEY first)
  bin.create();

  // Delete the warehouse (API-KEY needs to be set first)
  bin.delete();

  // Set API-KEY
  bin.apiKey = 'YOUR_API_KEY';

  // Set ACCESS-KEY (create/delete/read/update permissions can be set separately)
  bin.accessKey = 'YOUR_ACCESS_KEY';
  ```

- ##### Insert loading animation
  ```js
  let container = $axure('@target').$().get(0);
  let show = true;
  let options = {
    color:'#fff',
    opacity:0.2,
    scale:1.2,
    delay:0,
    dur:1,
    blending:'difference'
  };
  axlib.loading(container, show, options);
  ```

- ###### Load resources only once to avoid repeated loading
  ```js
  axlib.loadOnce({
    urls: [
      'https://ax.minicg.com/element/vue.min.js',
      'https://ax.minicg.com/element/index.css',
      'https://ax.minicg.com/element/index.js',
    ],
    lock: 'Element', // variable lock id, subject to variable name naming rules (cannot start with a number, special characters only $ and _ are accepted)
    callback: ()=> { console.log(window.Vue) }
  })
  ```


- ##### Get one or more components
  ```js
  // Native node (window.getEl === axlib.getEl)
  const [sym1, sym2, sym3] = getEl('sym1', 'sym2', 'sym3');
  const sym1 = axlib.getEl('sym1');

  // axure object
  const [sym1, sym2, sym3] = $axure.getEl('sym1', 'sym2', 'sym3');

  // jquery object
  const [sym1, sym2, sym3] = $.getEl('sym1', 'sym2', 'sym3');
  ```

- ##### Get a single component
  ```js
  let sym1 = $axure('@sym1').getEl();
  ```

- ##### Get multiple components
  ```js
  /*
   * The example code is equivalent to:
   * let sym1 = $axure('@sym1');
   * let sym2 = $axure('@sym2');
   * let sym3 = $axure('@sym3');
   */
  let { sym1, sym2, sym3 } = axlib.find('sym1', 'sym2', 'sym3');
  ```

- ##### Get a single component id
  ```js
  let id = $axure('@sym1').getId();
  ```

- ##### Save text file
  ```js
  // The browser will prompt you to download the file
  let text = 'Hello Axlib!';
  let fileName = 'hello.txt';
  axlib.saveTextFile(text, fileName);
  ```

- ###### Dynamically insert ES Module code
  ```js
  axlib.insertJsModule(`
    import { createApp } from 'https://unpkg.com/petite-vue?module';
    console.log(createApp);
  `);
  ```
