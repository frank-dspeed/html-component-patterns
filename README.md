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
https://codepen.io/frank-dspeed/pen/jOPyOVY 

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


lib/tag-html.mjs@0.0.5
```js
import { createExpressionWithTypeArguments } from 'typescript';

export { isEqual,isType, isString, isObject, isNumber, isStringObject, isPromise, isFunction } from './is.mjs'
export { HTMLElement } from './html-element.mjs'
export const escapeHtml = s => (s + '').replace(/[&<>"']/g, m => ({
    '&': '&amp;', '<': '&lt;', '>': '&gt;',
    '"': '&quot;', "'": '&#39;'
})[m]);

// is helper
//Returns true if it is a DOM node
export const isNode = o => isObject(Node) ? o instanceof Node : 
    o && isObject(o) && isNumber(o.nodeType) && isString(o.nodeName)
  
//Returns true if it is a DOM element    
const isElement = o => isObject(HTMLElement) ? o instanceof HTMLElement : //DOM2
    o && isObject(o) && o !== null && o.nodeType === 1 && isString(o.nodeName)
// needs benchmark if this is faster obj.constructor.toString()
const protoStr = obj => Object.prototype.toString.call(Object.getPrototypeOf(obj))
const isHtmlElement = obj => ((x)=> x.indexOf('HTML') > -1 && x.indexOf('Element') > -1)(protoStr(obj))
let attrAsString = `<div ${data.map(item => `data-attr="${item}")`).join(' ')}>` 
//Render Methods
// const html = htmlAdapter
// litHtml(html``)
export const htmlAdapter = (...args) => args
export const asRaw  = fn=> (strArr, ...valArr) => fn(strArr.raw,...valArr)
//const html = asRaw(litHtml)
//html`<h1>LitHTMLRAW</h1>`
//We should detect if we have arguments length 1 if yes and that is array of array 
//if array has no .raw it comes from processor!
//if arguments === 1 and array[0] === array
const curriedArray = () => arguments.length === 1
    ? arguments[0].raw
        ? arguments
        : arguments
    : ''
/**
 * Tagged Template Literal that returns string value
 * @param {Array<String>} strArr 
 * @param {...*} valArr
 * @returns {String} string
 */
export const html = (...args) => renderString(...args)
export const raw = (str,...args) =>  [str.raw,...args]
export const renderString = (...args) => renderLiteral(...args)
export const renderStringRaw = (...args) => renderString(...raw(...args))


export const removeNewLine = s=>s.replace(/(?:\n(?:\s*))+/g, ' ')
// Should be moved into tagged-template-strings
export const inline = (strs, ...vals) => strs.map(removeNewLine);

/**
 * OneLine(literals: TemplateStringsArray, ...placeholders: any[]): string
 * minifys a template while it compiles should be used with string only
 * @param strings 
 * @param keys 
 * @returns {String} string
 */
export const oneline = (strArr, ...valArr) => removeNewLine(strArr
   .reduce((acc, part, i) => acc + part + (valArr[i] || '') , ''))
   .trim()
   
export const renderLiteral = (strArr,...valArr) => strArr
    .map((strItm, i) => `${strItm}${valArr[i] ? `${valArr[i]}` : ''}`) 
    .join('')
// Promise Support returns a promise that resolved to html``result with all values resolved
// If one Promise Rejects without Handling it via catch it will throw?
// This allows timeout feature to return a template in a given time.
export const htmlPromise = (strArr, ...valArr) => Promise.all(valArr).then(vA => html(strArr, vA));

export const renderToElement = (strArr, ...valArr) => {
    return el => {
        el.innerHTML = processTemplate(strArr, ...valArr)
        replacePlaceholderNodes(el)
    }
}

/**
 * setPropertys on a Elemenet befor it is inserted into the if it has component property it will set it on that.
 * @param props 
 */
export const setProps = props => props
export const getComponentResult = c => typeof c.render === 'function' ? c.render() : typeof c === 'function' ? c() : c;
export const getRenderTarget = el => (typeof el.shadowRoot !== 'undefined' && typeof el.shadowRoot !== null) ? el.shadowRoot.innerHTML : typeof el.innerHTML !== 'undefined' ? el.innerHTML : el;

// could maybe look if used in the browser and directly use 
// createTemplateElement.
export const render = (component, el) => {
    const result = getComponentResult(component)
    if (!el) { return result; }
    let target = getRenderTarget(el)
    // We Should support Async Components Promise, iterator, Stream.
        
    if (isFunction(target)) {
        return target(result)
    }

    target = result;
    //This allows sync use of render directly
    return el;
};

export const renderAsync = async (component, el) => {
    const result = getComponentResult(component)
    return Promise.resolve(result).then(getComponentResult).then(r => {
        if (!el) { return result; }
        const target = getRenderTarget(el)
        if (isFunction(target)) {
            return target(r)
        }
        target = result;
        return el
    })
}
// Maybe deprecated
export const append = (x,el) => {
    const result = getComponentResult(component)
    if (!el) { return result; }
    // Should use browsers append method if possible
    const target = getRenderTarget(el);
    target = target+result;
    return el;
}

// Renders a Component as it would be a custom-element to allow components with element defintions
export const renderAsElement = c => `<${getTagName(c)}>${render(c)}</${getTagName(c)}>`
export const asElement = c => `<${getTagName(c)}></${getTagName(c)}>`
export const appendElement = (el,i) => el.replace('><', `>${i}<`)
// tackes a Class and registers a element if none is registered it will register it and return it
export const createElementDefinition = (x,base=typeof HTMLElement !== 'undefined' ? HTMLElement : class { }) => {
    /*
        Concepts we don't assign any methods to the prototype of the new Class only the component
        this allows to call the component with new inside of the element if needed via
        connectedCallback. it leaves the component untouched.
    */
    // use x.name as class name
    const isClass = Object.keys(Object.getPrototypeOf(x)).length === 0
    //class.toString() it maybe
    //if class extends other class mixin that.
    const newClass = new Function(`return class ${x.name} extends base {}`)()
    newClass.prototype.component = x
    
    /*
        Alternativ would be to copy stuff over to the new class
        This is discuraged as it leads to errors if the coder
        is not aware of all details
    */
    //if isClass we need to copy the prototype else we copy the object.
    // maybe can be object assign ? but that would take constructor?
    //Object.keys(x.prototype).map(m => newClass.prototype[m] = x.prototype[m])
    //connectedCallback() { this.innerHTML = this.component.render() }
    
    /*
        third method isomorphic class that is based on ifHTMLElement or mixin
        this don't needs this createElement Method it is a Element if needed.
    */
    
    //Object.setPrototypeOf(Element.prototype, x.prototype)
    return newClass
};

// Stream Processing binding (**optional**) you can always go the react way with render()
class streamElement {
    view(ctx) {
        const color = ctx.color
        return html`<input style="background-color: ${color};">`;
    }
    connectedCallback() {
        // Setup State
        this.color = 'green'
        //Render view if needed supply propertys via arguments
        this.innerHTML = this.innerHTML.length === 0 ? this.view(this) : this.innerHTML;
        //select element or elements for bindings to this state this.querySelector('')
        setTimeout(() => {
            this.setAttribute("class", "democlass");
            this.style.backgroundColor = 'red'    
        },4000)
        
        //Update <tag attribute=value>
        //Update <tag attribute>
        //get attribute as part
        //this.attribute
    }
}
/*
    <stream-element>${value}<stream-element>
*/
// Advanced utils helpers 
export function TagToStr(strings, ...values) {
    let i = 0
    return strings
      .map((s,i)=>(i === strings.raw.length-1) ? strings.raw[i] : strings.raw[i]+'${'+i+'}')
      .join('')
}
//console.log(TagToStr`${me}t4 string text line 1 \n ${me} string text line 2 ${me} me`);

export const runInContext = (source,ctx) => {
    const [keys, vals] = ObjToArrays(ctx)
    return Function(keys, source).apply(ctx,vals);
}
export const StrToTag = str => {
    const source = `return (() => \`${str}\`)()`;
    return runInContext(source,ctx)
}
// StrToTag Supporting async values in the ctx
export const strToTagPromise = async str => {
    const source = `return ((async () => \`${str.replace(/\${/g, '${await ')}\`))()`;
    return runInContext(source,ctx)
}

// Accepts only Objects
// is used as middleware in tagged template literals to reconstruct the keys for a template
export const ObjToArrays = obj => { 
    const arrays = [Object.keys(obj), Object.keys(obj).map(k=>obj[k])] // [[...keysAsString],[...values]]
    //for (let key in obj) { if (obj.hasOwnProperty(key)) { arrays[0].push(key); arrays[1].push(obj[key]); } };
    return arrays; // const [keys,vals] = arrays
}
// ObjToArrays reversed 
export const ArraysToObject = arrays => {
    const [keys, vals] = arrays;
    const obj = {}
    keys.map((key, i) => obj[key] = vals[i]);
}

// That means it is registered as it follows rule to include registration?
// Take MyComponent and return render if exist wrapp that result by its my-component tag.
/**
   A Custom Element gets registered with a tag 
   in the string representation a element also needs a tag
   so a isomorphic way to register or render the component
   to a tag is essential only the main app view don't needs a tag 
   as it produces the <html> or its tag could even be html
*/
// converts `YourString` into `your-string`
export const upperCamelCaseToSnakeCase = string => string
    .replace(/^([A-Z])/, $1 => $1.toLowerCase())
    .replace(/([A-Z])/g, $1 => "-" + $1.toLowerCase());

// converts `your-string` into `YourString`
export const snakeCaseToUpperCamelCase = string => string
    .toLowerCase()
    .replace(/^([a-z])/, $1 => $1.toUpperCase())
    .replace(/\-./g, $1 => $1.substring(1, 2).toUpperCase());

// converts your-string into `yourString`
export const snakeCaseToLowerCamelCase = string => snakeCaseToUpperCamelCase(string).replace(/^([a-z])/, $1 => $1.toLowerCase())

export const getName = Class => Class.name !== 'undefined' ? Class.name : Class.constructor.name !== 'undefined' ? Class.constructor.name : new Error('Class has no name property')
export const getTagName = Class => upperCamelCaseToSnakeCase(getName(Class))
export const getClassName = tagName => snakeCaseToUpperCamelCase(tagName)

export const defineComponentElement = async componentClass => {
    if (typeof customElements === 'undefined') {
        //here could be logic to express that component is a Element.
        //but that should be not done as this breaks other concepts
        //This should get used by Components to register them self.
        //or to register additional defintions for a component. that has none.
        return
    }
    const defintion = isFunction(componentClass) ? componentClass() : componentClass   
    const tagName = getTagName(defintion)
    if (typeof customElements.get(tagName) === 'undefined') {
        customElements.define(tagName, defintion);
    }
}

// Works only in the dom
const processTemplate = (strArr, ...valArr) => strArr
    .map((s, i) => `${s}${valArr[i] ? isElement(valArr[i]) ? `<unknown-html-element-placeholder class="${i}"></unknown-html-element-placeholder>` : `${valArr[i]}` : s}`)
    .join('')

const replacePlaceholderNodes = (DOMNode,valArr) => DOMNode.querySelectorAll('unknown-html-element-placeholder')
    .forEach(el => el.parentNode.replaceChild(valArr[el.className], el))

export const createTemplateElement = (strArr, ...valArr) => {
    const template = document.createElement('template')
    template.innerHTML = processTemplate(strArr, ...valArr)
    replacePlaceholderNodes(template.content)

    return el => {
            //With DomC rehydrate cloneNode fals reuse template
            const node = template.content.cloneNode(true);
            el.appendChild(node);
            return node;
        }
}
```
