# Sign Table

Official announcement on Arltools : https://arltools.xyz/p/143

## Prerequisties

- This is being used along with the `react-markdown` package. You can replace the `Markdown` component by the `KaTeX` component so you can render LaTeX within cells

## Examples

How to render sign tables with a custom syntax (close to matrices in LaTex).

You can try it here : https://arltools.xyz, it can be used along with the markdown syntax in Arltools. The code attached in this markdown will allow you to replace this specific syntax (using strings) but you can use `reactStringReplace` package if you don't want to convert your components to string for some reason.

Here is an example with a very simple function

<img width="710" alt="image" src="https://github.com/BenchBadr/sign-table/assets/78297845/b5db5907-687a-4fe8-9914-45b927dc1d5d">

<img width="594" alt="image" src="https://github.com/BenchBadr/sign-table/assets/78297845/6120ef04-adf2-4c8d-993f-2fc21e87ba97">

The sign table above has been made with the following script :

```
x & -\infty & -3 & 0 & 4 & +\infty
x+3 & - | & + & +
x-4 & + & +  |& - 
f(x) & - |& + & - 
```

- Other example with forbidden values

```
x & -\infty & -1 & 2 & +\infty
x+1 & - | & + & +
4-2x & + & + | &-
(x+1)(4-2x) &  - |& + |& - 
```

## The syntax

- `&` (ampersand) to split cells
- Simply skip a line to change row, no `\\` needed unlike the usual $\LaTeX$ syntax.
- Put a `|` before the `&` to add a zero as a line separator
- Put a `#` before the `&` to add double-lines, standing for forbidden values

Note that you can also add a variation table using arrows and ampersands as separators as they are supported by LaTeX with `\nearrow` ($\nearrow$) and `\searrow` ($\searrow$). 
## Code


```jsx
children = children.replace(regex, (match, content) => {
          let contentNew = content;
          if (cls === 'sign') {
            try{
            contentNew = contentNew.split('\n').filter(line => line.trim() !== '').map(line => line.split('&'));
            const output = (
              <div className="sign">
                <div className="row">
                    {contentNew[0].map((element, index) => (
                      <div className="cell" key={index}><Markdown
                      remarkPlugins={[remarkMath, [remarkGfm, { singleTilde: false }]]}
                      rehypePlugins={[rehypeKatex, rehypeRaw]}
                      >{`$${element}$`}</Markdown></div>
                    ))}
                </div>
                  {contentNew.slice(1).map((line, rowIndex) => (
                    <div className="row" key={rowIndex}>
                      {line.map((element, columnIndex) => { 
                        let classCell = `cell ${element.includes('|') ?  ' ban' : ''} ${element.includes('#') ? ' zero' : ''}`;
                        return (
                        <div className={classCell} key={columnIndex}><Markdown
                        remarkPlugins={[remarkMath, [remarkGfm, { singleTilde: false }]]}
                        rehypePlugins={[rehypeKatex, rehypeRaw]}
                        >{`$${element.replace('#','').replace('|','')}$`}</Markdown></div>
                      )})}
                    </div>
                  ))}
              </div>
            );
            
            return ReactDOMServer.renderToStaticMarkup(output)
          } catch (error){
            console.log('invalid sign table')
          }
          } else {
          let MdComponent = <Markdown
            remarkPlugins={[remarkMath, [remarkGfm, { singleTilde: false }]]}
            rehypePlugins={[rehypeKatex, rehypeRaw]}
          >
            {contentNew}</Markdown>;
          return ReactDOMServer.renderToStaticMarkup(<div className={cls}>{MdComponent}</div>);
        }
      });
      });
      return children;
```
