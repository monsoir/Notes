# Problem Solved

### couldn’t be opened because you don’t have permission to view it.

In my case, bundle ID missed the "com.".

### When install angualrjs: `enoent ENOENT: no such file or directory`

run `npm init` first

### How to create a Cordova project

1. `cordova create <Project folder name> <Bundle ID> <App display name>`
2. Into the project folder `cd <Project folder name>`
3. `cordova platform add ios --save`
4. `cordova build` or for specific platform `cordova build ios`
5. `cordova run ios --target="iPhone-6s"`

### How to create a blank ionic project

- `ionic start \<App Name\> blank`

### How to set Chrome as the default running browser

- If the project folder does not have the file called `ionic.project`, then `ionic serve` first, this will generate the file.
- `vim ionic.project`, add this line `"defaultBrowser": "google chrome"`

### Digest

#### Block button
- Adding `button-block` to a button applies `display: block` display. A block button will however go 100% of its parent's width

```html
<button class="button button-block button-positive">
  Block Button
</button>
```

#### Full width block button

- not only applies `display: block`, but also removes borders on the left and right, and any border-radius which may be applied

