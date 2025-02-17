# Palette

<p class="description">The palette enables you to modify the color of the components to suit your brand.</p>

## Palette colors

The theme exposes the following palette colors (accessible under `theme.palette.`):

- _primary_ - used to represent primary interface elements for a user. It's the color displayed most frequently across your app's screens and components.
- _secondary_ - used to represent secondary interface elements for a user. It provides more ways to accent and distinguish your product. Having it is optional.
- _error_ - used to represent interface elements that the user should be made aware of.
- _warning_ - used to represent potentially dangerous actions or important messages.
- _info_ - used to present information to the user that is neutral and not necessarily important.
- _success_ - used to indicate the successful completion of an action that user triggered.

If you want to learn more about color, you can check out [the color section](/customization/color/).

## Default values

You can explore the default values of the palette using [the theme explorer](/customization/default-theme/?expand-path=$.palette) or by opening the dev tools console on this page (`window.theme.palette`).

{{"demo": "pages/customization/palette/Intentions.js", "bg": "inline", "hideToolbar": true}}

The default palette uses the shades prefixed with `A` (`A200`, etc.) for the secondary palette color,
and the un-prefixed shades for the other palette colors.

## Customization

You may override the default palette values by including a palette object as part of your theme.
If any of the:

- [`.palette.primary`](/customization/default-theme/?expand-path=$.palette.primary)
- [`.palette.secondary`](/customization/default-theme/?expand-path=$.palette.secondary)
- [`.palette.error`](/customization/default-theme/?expand-path=$.palette.error)
- [`.palette.warning`](/customization/default-theme/?expand-path=$.palette.warning)
- [`.palette.info`](/customization/default-theme/?expand-path=$.palette.info)
- [`.palette.success`](/customization/default-theme/?expand-path=$.palette.success)

palette color objects are provided, they will replace the default ones.

The palette color value can either be a [color](/customization/color/#2014-material-design-color-palettes) object, or an object with one or more of the keys specified by the following TypeScript interface:

```ts
interface PaletteColor {
  light?: string;
  main: string;
  dark?: string;
  contrastText?: string;
}
```

### Using a color object

The simplest way to customize a palette color is to import one or more of the provided colors
and apply them:

```js
import { createMuiTheme } from '@material-ui/core/styles';
import blue from '@material-ui/core/colors/blue';

const theme = createMuiTheme({
  palette: {
    primary: blue,
  },
});
```

### Providing the colors directly

If you wish to provide more customized colors, you can either create your own palette color,
or directly supply colors to some or all of the `theme.palette` keys:

```js
import { createMuiTheme } from '@material-ui/core/styles';

const theme = createMuiTheme({
  palette: {
    primary: {
      // light: will be calculated from palette.primary.main,
      main: '#ff4400',
      // dark: will be calculated from palette.primary.main,
      // contrastText: will be calculated to contrast with palette.primary.main
    },
    secondary: {
      light: '#0066ff',
      main: '#0044ff',
      // dark: will be calculated from palette.secondary.main,
      contrastText: '#ffcc00',
    },
    // Used by `getContrastText()` to maximize the contrast between
    // the background and the text.
    contrastThreshold: 3,
    // Used by the functions below to shift a color's luminance by approximately
    // two indexes within its tonal palette.
    // E.g., shift from Red 500 to Red 300 or Red 700.
    tonalOffset: 0.2,
  },
});
```

As in the example above, if the palette color contains custom colors using any of the
"main", "light", "dark" or "contrastText" keys, these map as follows:

- If the "dark" and / or "light" keys are omitted, their value(s) will be calculated from "main",
  according to the "tonalOffset" value.
- If "contrastText" is omitted, its value will be calculated to contrast with "main",
  according to the "contrastThreshold" value.

Both the "tonalOffset" and "contrastThreshold" values may be customized as needed.
The "tonalOffset" value can either be a number between 0 and 1, which will apply to both light and dark variants, or an object with light and dark variants specified by the following TypeScript type:

```ts
type PaletteTonalOffset =
  | number
  | {
      light: number;
      dark: number;
    };
```

A higher value for "tonalOffset" will make calculated values for "light" lighter, and "dark" darker.
A higher value for "contrastThreshold" increases the point at which a background color is considered
light, and given a dark "contrastText".

Note that "contrastThreshold" follows a non-linear curve.

### Example

{{"demo": "pages/customization/palette/Palette.js", "defaultCodeOpen": true}}

### Adding new colors

You can add new colors inside and outside the palette of the theme as follow:

```js
import { createMuiTheme } from '@material-ui/core/styles';

const theme = createMuiTheme({
  status: {
    danger: '#e53e3e',
  },
  palette: {
    primary: {
      main: '#0971f1',
      darker: '#053e85',
    },
    neutral: {
      main: '#5c6ac4',
    },
  },
});
```

If you are using TypeScript, you would also need to use [module augmentation](/guides/typescript/#customization-of-theme) for the theme to accept the above values.

<!-- tested with packages/material-ui/test/typescript/augmentation/paletteColors.spec.ts -->

```ts
declare module '@material-ui/core/styles' {
  interface Theme {
    status: {
      danger: React.CSSProperties['color'];
    };
  }

  interface Palette {
    neutral: Palette['primary'];
  }
  interface PaletteOptions {
    neutral: PaletteOptions['primary'];
  }

  interface PaletteColor {
    darker?: string;
  }
  interface SimplePaletteColorOptions {
    darker?: string;
  }
  interface ThemeOptions {
    status: {
      danger: React.CSSProperties['color'];
    };
  }
}
```

## Picking colors

Need inspiration? The Material Design team has built an [palette configuration tool](/customization/color/#picking-colors) to help you.

## Dark mode

Material-UI comes with two palette types, light (the default) and dark.
You can make the theme dark by setting `mode: 'dark'`.
While it's only a single value change, internally it modifies several palette values.

```js
const darkTheme = createMuiTheme({
  palette: {
    mode: 'dark',
  },
});
```

The colors modified by the palette type are the following:

{{"demo": "pages/customization/palette/DarkTheme.js", "bg": "inline", "hideToolbar": true}}

### User preference

Users might have specified a preference for a light or dark theme.
The method by which the user expresses their preference can vary. It might be a system-wide setting exposed by the Operating System, or a setting controlled by the User Agent.

You can leverage this preference dynamically with the [useMediaQuery](/components/use-media-query/) hook and the [prefers-color-scheme](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-color-scheme) media query.

For instance, you can enable the dark mode automatically:

```jsx
import * as React from 'react';
import useMediaQuery from '@material-ui/core/useMediaQuery';
import { createMuiTheme, ThemeProvider } from '@material-ui/core/styles';
import CssBaseline from '@material-ui/core/CssBaseline';

function App() {
  const prefersDarkMode = useMediaQuery('(prefers-color-scheme: dark)');

  const theme = React.useMemo(
    () =>
      createMuiTheme({
        palette: {
          mode: prefersDarkMode ? 'dark' : 'light',
        },
      }),
    [prefersDarkMode],
  );

  return (
    <ThemeProvider theme={theme}>
      <CssBaseline />
      <Routes />
    </ThemeProvider>
  );
}
```
