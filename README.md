# html-component-patterns
A Collection of Some Usefull Examples from my Component System Research.

# Classes & mixins 01.2020
https://codepen.io/frank-dspeed/pen/NWPXGwp?editors=0010 
```js
//A ES Module that runs in the browser and any other environment that returns HTMLElement Conditional
const ifHTMLElement = typeof HTMLElement !== 'undefined' ? HTMLElement : class HTMLElement { }

const MixinComponent = base => class Component extends base {
    // Used when not using extend or when calling super with arguments
    constructor(tag = '', template = () => '', data = {}) {
        super()
        this.tagName = tag
        this.template = template.bind(this)
        Object.assign(this, data)
        // if browser 
        // Should Choose to register the element.
        // Should use lit-html template and render
        // render(this.template(),this)
        
        // if node
        // this should return render to be compatible to HTMLElement
    }
    render() {
        const { template, tagName, data } = this
        const result = template(data)
        this.innerHTML = result
        return `<${tagName}>${result}</${tagName}>`
    }
}


// minimum component can be also customElement
// customElements can trigger actions on insert
// customElements can encapsulate dom.
class myComponent extends MixinComponent(ifHTMLElement) {
    tagName = 'my-tag'
    template(data) {
        return `${data}`
    }
    data = 'string'
}
/**
 * About values
 * they can come from user input
 * they can come from external source
 * they can be coded
 * when a value changes it should call render or not
 */
class myListComponent extends MixinComponent(ifHTMLElement) {
    tagName = 'my-tag'
    //data should be a array
    template(data) {
        return `${data}`
    }
    data = 'string'
}
// can render a component
// can expose some data
class pageComponent extends MixinComponent(ifHTMLElement) {
    tagName = 'my-tag'
    //data should be a array
    template(data) {
      //based on url return data
      //pageComponents returns render from similar components
      return `${this.pageComponent}`
    }
    data = {
	    get config() {
        return {
          title: "Game",
          componentName: "game-details",
          viewModel: () => ({
            gameId: value.from(this, "gameId"),
            session: value.from(this, "session"),
            gamePromise: value.to(this, "pagePromise")
          }),
          modulePath: "game/details/"
        }
      },
      async get pageComponent() {
        var modulepath = "bitballs/components/" + this.pageComponentConfig.modulePath;
        return import(modulepath).then(({Component}) => {
          return new Component({ viewModel: this.pageComponentConfig.viewModel() });
        });
      }      
    }

}


class appComponent extends MixinComponent(ifHTMLElement) {
    tagName = 'my-tag'
    //data should be a array
    template(data) {
      let pageComponent = new pageComponent()  
      return `<html lang="en">
	<head>
		<base href="/">
		<meta name="viewport" content="width=device-width, initial-scale=1">
		<title>${pageComponent.title}</title>
	</head>
	<body>
		<div class="container">
			${new navigationComponent()}
      ${pageComponent} 
		</div>
	</body>
</html>`
    }
    data = 'string'
}
```

A function that is used to convert cases between tags

```js
const snakeCaseToUpperCamelCase = string => string
        .replace(/\-./g, $1 => $1.substring(1, 2).toUpperCase());
```

# Custom Element with Template string literals
https://codepen.io/frank-dspeed/pen/jOEyMop?editors=1010 18.12.2019
```js
const html = (strArr, ...valArr)=>strArr
  // add a Stream Emitter here
  .map((e, i) => `${e}${valArr[i] ? valArr[i] : ''}`) 
  .join('')

// Components can fetch data 
// returns static method Component.extends(base,class me {})


// here you define the custom-elements used in the
// Component.
const importPromises = [];
const isBrowser = true;
if(isBrowser){

Promise.all(importPromises)
  .then(()=>console.log('all Elements are loaded'))
  .catch(e=>console.log('Could Not Upgrade All ',e))
}

class streamElement extends HTMLElement {
    view() {
        // a directive for binding will be needed
        // It should return the current part binded
        return html`
          <input style="background-color: ${this.color};">
        `;
    }
    get color(){
      const input = this.querySelector('input')
      return input ? input.style.backgroundColor : 'green';
    }
    set color(color){
      const input = this.querySelector('input')
      if(input) {
        input.setAttribute("class", "democlass");
        input.style.backgroundColor = color
      }
    }
    connectedCallback() {
        // Setup State use with setter
        //render(view);
        this.color = 'green'
        //Render view if needed supply propertys via arguments
        this.innerHTML = this.innerHTML.length === 0 ? this.view(this) : this.innerHTML;
        //select element or elements for bindings to this state this.querySelector('')
      console.log('init')  
      setTimeout(()=>{
        //this.color='red'
        const input = this.querySelector('input')
        if(input) {
          input.setAttribute("class", "democlass");
          input.style.backgroundColor = color
        }
      },4000)
        
        //Update <tag attribute=value>
        //Update <tag attribute>
        //get attribute as part
        //this.attribute
    }
}
customElements.define('my-el',streamElement)
```


Diffrent Execution Async CJS & ESM in browsers

```html
<script async>
  import('https://unpkg.com/tag-html@0.0.11/tag-html.mjs')
console.log('First inline CJS async')
</script>
<script type="module">
   import {html} from 'https://unpkg.com/tag-html@0.0.11/tag-html.mjs'
  console.log('First inline  ESM no async')
</script>

<script type="module" async>
   import {html} from 'https://unpkg.com/tag-html@0.0.11/tag-html.mjs?new'
  console.log('First inline ESM async')
</script>
<script defer>
  import('https://unpkg.com/tag-html@0.0.11/tag-html.mjs')
console.log('First inline CJS defer')
</script>
<script type="module">
   import {html} from 'https://unpkg.com/tag-html@0.0.11/tag-html.mjs?new2'
   console.log('Secund inline  ESM no async')
</script>
<script type="module" async>
   import {html} from 'https://unpkg.com/tag-html@0.0.11/tag-html.mjs?new4'
  console.log('Secund inline ESM async')
</script>
<script type="module">
   import {html} from 'https://unpkg.com/tag-html@0.0.11/tag-html.mjs?new2'
   console.log('first inline  ESM defer')
</script>
<script async>
  import('https://unpkg.com/tag-html@0.0.11/tag-html.mjs?new3')
  console.log('Secund inline CJS async')
</script>
```
