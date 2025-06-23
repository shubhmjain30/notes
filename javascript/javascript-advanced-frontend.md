[[javascript-roadmap.md]]

# Advanced Frontend Concepts & Design Patterns

## Introduction

This section focuses on advanced frontend topics, including React hooks, essential design patterns, and principles for building scalable frontend systems. It is especially relevant for senior and lead developer roles.

### Knowledge Points

#### React Hooks: useEffect vs. useLayoutEffect

Understanding the timing differences between these hooks is crucial:

```javascript
// useEffect runs after browser paint
// Preferred for most cases
function ComponentWithEffect() {
	const [count, setCount] = useState(0);

	useEffect(() => {
		// Runs after the component renders and the browser paints
		console.log("Effect ran, count:", count);

		return () => {
			// Cleanup function runs before next effect or unmount
			console.log("Cleaning up effect");
		};
	}, [count]); // Only re-run when count changes

	return (
		<button onClick={() => setCount((c) => c + 1)}>Count: {count}</button>
	);
}

// useLayoutEffect runs before browser paint
// Use for DOM measurements and visual updates that must be synchronous
function ComponentWithLayoutEffect() {
	const [width, setWidth] = useState(0);
	const elementRef = useRef(null);

	useLayoutEffect(() => {
		// Runs synchronously after DOM mutations but before browser paint
		if (elementRef.current) {
			const newWidth = elementRef.current.getBoundingClientRect().width;
			setWidth(newWidth); // This update happens before the user sees anything
		}
	}, [elementRef.current]);

	return (
		<div>
			<div ref={elementRef}>Measure my width</div>
			<p>Width: {width}px</p>
		</div>
	);
}
```

#### Other Essential React Hooks

Modern React development relies on these core hooks:

```javascript
function EssentialHooks() {
	// useState - Add state to functional components
	const [count, setCount] = useState(0);

	// useRef - Persistent value that doesn't cause re-renders
	const inputRef = useRef(null);
	const renderCount = useRef(0);

	// useCallback - Memoize functions to prevent unnecessary re-renders
	const handleClick = useCallback(() => {
		setCount((c) => c + 1);
		inputRef.current?.focus();
	}, []);

	// useMemo - Memoize expensive calculations
	const expensiveValue = useMemo(() => {
		console.log("Computing expensive value");
		return count * count * count;
	}, [count]);

	// Track renders without affecting them
	useEffect(() => {
		renderCount.current += 1;
		console.log(`Rendered ${renderCount.current} times`);
	});

	return (
		<div>
			<p>Count: {count}</p>
			<p>Expensive calculation: {expensiveValue}</p>
			<input ref={inputRef} placeholder="Focus on click" />
			<button onClick={handleClick}>Increment & Focus</button>
		</div>
	);
}
```

#### Core Design Patterns: Singleton, Observer, Factory

Essential patterns for scalable frontend architecture:

```javascript
// Singleton Pattern - One instance shared throughout the app
class ThemeManager {
	static instance;

	constructor() {
		if (ThemeManager.instance) {
			return ThemeManager.instance;
		}

		this.theme = "light";
		ThemeManager.instance = this;
	}

	getTheme() {
		return this.theme;
	}

	setTheme(newTheme) {
		this.theme = newTheme;
		this.notifyListeners();
	}

	// Observer pattern integration
	listeners = [];

	subscribe(callback) {
		this.listeners.push(callback);
		return () => {
			this.listeners = this.listeners.filter((cb) => cb !== callback);
		};
	}

	notifyListeners() {
		this.listeners.forEach((callback) => callback(this.theme));
	}
}

// Usage
const themeManager1 = new ThemeManager();
const themeManager2 = new ThemeManager();
console.log(themeManager1 === themeManager2); // true

// Factory Pattern - Create objects without specifying exact class
class Button {
	constructor(text, color) {
		this.text = text;
		this.color = color;
	}

	render() {
		return `<button style="color: ${this.color}">${this.text}</button>`;
	}
}

class Link {
	constructor(text, url) {
		this.text = text;
		this.url = url;
	}

	render() {
		return `<a href="${this.url}">${this.text}</a>`;
	}
}

// Factory
class UIFactory {
	createElement(type, config) {
		switch (type) {
			case "button":
				return new Button(config.text, config.color);
			case "link":
				return new Link(config.text, config.url);
			default:
				throw new Error(`Element type ${type} not supported`);
		}
	}
}

// Usage
const factory = new UIFactory();
const submitButton = factory.createElement("button", {
	text: "Submit",
	color: "blue",
});
const homeLink = factory.createElement("link", { text: "Home", url: "/" });
```
