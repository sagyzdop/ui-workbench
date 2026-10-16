# References

Material to look at while working in the workbench, and nothing else.

Code snippets, screenshots, a spec, a file out of a library. Any file type. None
of it is rendered: the entry globs do not match this folder, so a `.tsx` here is
never mounted as a demo, and nothing imports it.

Keep the project's production stylesheet clean. This folder sits under the
workbench's Vite root, which is Tailwind's automatic source base, so a `.tsx` here
has its utilities compiled into the app's CSS unless the stylesheet says otherwise:

```css
@source not "../ui-workbench/references";
```

A prototype made from a reference still goes in `components/` or
`route-screens/`, written against the project's own components.
