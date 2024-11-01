# Terminal Formatters

This is utility library for Pure Terminal and (that is compatible with jQuery Terminal),
that converts text or markup into Terminal low level format.

## Installation

Using NPM:

```bash
npm install @pure-terminal/formatters
```

Using Yarn:

```bash
yarn add @pure-terminal/formatters
```

## Usage

Using builtin formatters

```javascript
import { Formatter, formatters } from '@pure-terminal/formatters';

const input = '<white>Hello</white>';

const formatter = new Formatter({ formatters: [formatters.xml, formatters.nested] });

const output = formatter.format(input);

console.log(output);
```

Creating your own formatter using Peggy parser.

```typescript
import { Formatter } from '@pure-terminal/formatters';

const grammar = `
start = arr:(content / [\n])+ {
    return arr.join('');
}

content = text:(image / inline_text)+ nl:[\n]? { return text.join('') + (nl || ''); }

inline_text = link / [^\n]

image = '!' + link:inline_link {
    return '[[@;;;;' + link.url + ']' + link.text + ']';
}

link = link:inline_link {
    return '[[!;;;;' + link.url + ']' + link.text + ']';
}

inline_link = '[' text:[^\]]* ']' '(' url:[^)]+ ')' {
    return {
        text: text.join(''),
        url: url.join('')
    };
}
`;
const parser = peggy.generate(grammar.innerHTML);
const format = (input: string) => {
    return [parser.parse(input), 0];
};

const formatter = new Formatter({ formatters: [format] });

formatter.format('This is [link](https://terminal.jcubic.pl)');
// [[!;;;;https://terminal.jcubic.pl]link]
```

Above parser creates Markdown links and images.

## License
Licensed under [MIT](http://opensource.org/licenses/MIT) license<br/>
Copyright (c) 2024 [Jakub T. Jankiewicz](https://jcubic.pl/me)
