CSS tips  
https://denic.hashnode.dev/css-tips-you-wont-see-in-most-tutorials

JS-free modal  
https://twitter.com/denicmarko/status/1350761109360414721
https://codepen.io/denic/pen/ZEbKgPp
```css
/* If you like this, be sure to ❤️ it. */
.wrapper {
  height: 100vh;
  /* This part is important for centering the content */
  display: flex;
  align-items: center;
  justify-content: center;
  /* End center */
  background: -webkit-linear-gradient(to right, #834d9b, #d04ed6);
  background: linear-gradient(to right, #834d9b, #d04ed6);
}

.wrapper a {
  display: inline-block;
  text-decoration: none;
  padding: 15px;
  background-color: #fff;
  border-radius: 3px;
  text-transform: uppercase;
  color: #585858;
  font-family: 'Roboto', sans-serif;
}

.modal {
  visibility: hidden;
  opacity: 0;
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(77, 77, 77, .7);
  transition: all .4s;
}

.modal:target {
  visibility: visible;
  opacity: 1;
}

.modal__content {
  border-radius: 4px;
  position: relative;
  width: 500px;
  max-width: 90%;
  background: #fff;
  padding: 1em 2em;
}

.modal__footer {
  text-align: right;
  a {
    color: #585858;
  }
  i {
    color: #d02d2c;
  }
}
.modal__close {
  position: absolute;
  top: 10px;
  right: 10px;
  color: #585858;
  text-decoration: none;
}
```

```html
<div class="wrapper">
    <a href="#demo-modal">Open Demo Modal</a>
</div>

<div id="demo-modal" class="modal">
    <div class="modal__content">
        <h1>CSS Only Modal</h1>

        <p>
            You can use the :target pseudo-class to create a modals with Zero JavaScript. Enjoy!
        </p>

        <div class="modal__footer">
            Made with <i class="fa fa-heart"></i>, by <a href="https://twitter.com/denicmarko" target="_blank">@denicmarko</a>
        </div>

        <a href="#" class="modal__close">&times;</a>
    </div>
</div>
```

Close with outside click
https://codepen.io/xirclebox/pen/jOqmJOr
```css
$opacity-transition: opacity $speed ease-in-out;

.modal-header {
	align-items: baseline;
	display: flex;
	justify-content: space-between;
}

.close {
	background: none;
	border: none;
	cursor: pointer;
	display: flex;
	height: 16px;
	text-decoration: none;
	width: 16px;

	svg {
		width: 16px;
	}
}

.modal-wrapper {
	align-items: center;
	background: rgba(0, 0, 0, 0.7);
	bottom: 0;
	display: flex;
	justify-content: center;
	left: 0;
	position: fixed;
	right: 0;
	top: 0;
}

#modal {
	opacity: 0;
	transition: $opacity-transition;
	visibility: hidden;

	&:target {
		opacity: 1;
		visibility: visible;

		.modal-body {
			opacity: 1;
			transform: translateY(1);
		}
	}

	.modal-body {
		max-width: 500px;
		opacity: 0;
		transform: translateY(-100px);
		transition: $opacity-transition;
		width: 100%;
		z-index: 1;
	}
}

.outside-trigger {
	bottom: 0;
	cursor: default;
	left: 0;
	position: fixed;
	right: 0;
	top: 0;
}

.button__link {
	text-decoration: none;
}

```

```html
<div class="card">
	<div class="content-wrapper">
		<h2 class="heading">CSS Modal</h2>
		<p>This demo shows how to open a modal without using JavaScript. While it's a cool trick, it's not 100% accessible. JavaScript is needed to set focus on the modal and to create a focus trap.</p>
	</div>
	<a href="#modal" role="button" class="button button__link">Modal Trigger</a>
</div>

<!-- Modal -->
<div class="modal-wrapper" id="modal">
	<div class="modal-body card">
		<div class="modal-header">
			<h2 class="heading">Modal Header</h2>
			<a href="#!" role="button" class="close" aria-label="close this modal">
				<svg viewBox="0 0 24 24">
					<path d="M24 20.188l-8.315-8.209 8.2-8.282-3.697-3.697-8.212 8.318-8.31-8.203-3.666 3.666 8.321 8.24-8.206 8.313 3.666 3.666 8.237-8.318 8.285 8.203z" />
				</svg>
			</a>
		</div>
		<p>Simple example using the <code>:target</code> selector to open a modal.</p>
	</div>
	<a href="#!" class="outside-trigger"></a>
</div>
```

