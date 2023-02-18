# 目录

```antd
const { NavList } = components;

const data = [{
	title: 'Chapter 0 Introduction',
	desc: 'The Rust programming language helps you write faster, more reliable software.'
}, {
	title: 'Chapter 1 Getting Started',
	desc: 'The first step is to install Rust. We’ll download Rust through `rustup`, a command line tool for managing Rust versions and associated tools. You’ll need an internet connection for the download.'
}, {
	title: 'Chapter 2 Programming a Guessing Game',
	desc: 'We’ll implement a classic beginner programming problem: a guessing game.'
}]

ReactDOM.createRoot(el).render(<NavList data={data} />)
```

