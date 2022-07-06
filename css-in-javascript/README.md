# Airbnb CSS-in-JavaScript Style Guide CSS-in-JavaScript JS中CSS的样式指导

*A mostly reasonable approach to CSS-in-JavaScript* 

## Table of Contents 目录

1. [Naming](#naming) 命名方式
1. [Ordering](#ordering) 顺序
1. [Nesting](#nesting) 嵌套
1. [Inline](#inline) 内嵌
1. [Themes](#themes) 主题

## Naming 命名方式

  - Use camelCase for object keys对象键 (i.e. "selectors").

    > Why? We access these keys as properties on the `styles` object in the component,在组件的风格对象中的`styles`我们通过访问这些键作为属性 so it is most convenient to use camelCase.很方便使用camelCase

    ```js
    // bad👎👎👎
    {
      'bermuda-triangle': {
        display: 'none',
      },
    }

    // good👍👍👍
    {
      bermudaTriangle: {
        display: 'none',
      },
    }
    ```

  - Use an underscore for modifiers to other styles.用`_`做其他样式的修饰符

    > Why? Similar to BEM,[`BEM`](http://getbem.com/) this naming convention makes it clear that the styles are intended to modify the element preceded by the underscore.命名公约，样式用于修改前面带有下划线`_`的元素 Underscores do not need to be quoted,`_`不需要被quoted so they are preferred over other characters, such as dashes.因此他们优先于其他字符，例如破折号

    ```js
    // bad👎👎👎
    {
      bruceBanner: {
        color: 'pink',
        transition: 'color 10s',
      },

      bruceBannerTheHulk: {
        color: 'green',
      },
    }

    // good👍👍👍
    {
      bruceBanner: {
        color: 'pink',
        transition: 'color 10s',
      },
      //`_`做其他样式的修饰
      bruceBanner_theHulk: {
        color: 'green',
      },
    }
    ```

  - Use `selectorName_fallback` for sets of fallback styles.

    > Why? Similar to modifiers, keeping the naming consistent helps reveal the relationship of these styles to the styles that override them in more adequate browsers. 保证命名方式的一致性，帮助揭示这些样式的相互关系，以及互相之间的显示覆盖

    ```js
    // bad👎👎👎
    {
      muscles: {
        display: 'flex',
      },

      muscles_sadBears: {
        width: '100%',
      },
    }

    // good👍👍👍
    {
      muscles: {
        display: 'flex',
      },

      muscles_fallback: {
        width: '100%',
      },
    }
    ```

  - Use a separate selector for sets of fallback styles.为后备样式设定独立的分离选择器

    > Why? Keeping fallback styles contained in a separate object clarifies their purpose, which improves readability.对独立对象声明保留后备样式以提高可读性

    ```js
    // bad👎👎👎
    {
      muscles: {
        display: 'flex',
      },

      left: {
        flexGrow: 1,
        display: 'inline-block',
      },

      right: {
        display: 'inline-block',
      },
    }

    // good👍👍👍
    {
      muscles: {
        display: 'flex',
      },

      left: {
        flexGrow: 1,
      },

      left_fallback: {
        display: 'inline-block',
      },

      right_fallback: {
        display: 'inline-block',
      },
    }
    ```

  - Use device-agnostic names (e.g. "small", "medium", and "large")使用与设备无关的名称 to name media query breakpoints.命名媒体查询断点

    > Why? Commonly used names like "phone", "tablet", and "desktop" do not match the characteristics of the devices in the real world. Using these names sets the wrong expectations. 类似"phone", "tablet", and "desktop"一类的常用名并不能与现实世界中设备的特征匹配，使用这些名称集设定了错误的预期

    ```js
    // bad👎👎👎
    const breakpoints = {
      mobile: '@media (max-width: 639px)',
      tablet: '@media (max-width: 1047px)',
      desktop: '@media (min-width: 1048px)',
    };

    // good👍👍👍
    const breakpoints = {
      small: '@media (max-width: 639px)',
      medium: '@media (max-width: 1047px)',
      large: '@media (min-width: 1048px)',
    };
    ```

## Ordering 顺序

  - Define styles after the component.先定义组件后定义样式

    > Why? We use a higher-order component to theme our styles,主题样式 which is naturally used after the component definition.自然被组件定义 Passing the styles object directly to this function reduces indirection. 直接传递样式对象到函数中减少间接

    ```jsx
    // bad👎👎👎
    const styles = {
      container: {
        display: 'inline-block',
      },
    };

    function MyComponent({ styles }) {
      return (
        <div {...css(styles.container)}>
          Never doubt that a small group of thoughtful, committed citizens can
          change the world. Indeed, it’s the only thing that ever has.
        </div>
      );
    }

    export default withStyles(() => styles)(MyComponent);

    // good👍👍👍
    function MyComponent({ styles }) {
      return (
        <div {...css(styles.container)}>
          Never doubt that a small group of thoughtful, committed citizens can
          change the world. Indeed, it’s the only thing that ever has.
        </div>
      );
    }

    export default withStyles(() => ({
      container: {
        display: 'inline-block',
      },
    }))(MyComponent);
    ```

## Nesting 嵌套

  - Leave a blank line between adjacent blocks at the same indentation level.在相邻块间留一个blank line

    > Why? The whitespace improves readability and reduces the likelihood of merge conflicts.空白空间提高可读性，减少merge code时会发生的冲突

    ```js
    // bad👎👎👎
    {
      bigBang: {
        display: 'inline-block',
        '::before': {
          content: "''",
        },
      },
      universe: {
        border: 'none',
      },
    }

    // good👍👍👍
    {
      bigBang: {
        display: 'inline-block',
        <!-- leave a blank line -->
        '::before': {
          content: "''",
        },
      },

      universe: {
        border: 'none',
      },
    }
    ```

## Inline 内嵌

  - Use inline styles for styles that have a high cardinality内嵌样式基数 (e.g. uses the value of a prop) and not for styles that have a low cardinality.

    > Why? Generating themed stylesheets can be expensive,生成带主题的样式表很昂贵 so they are best for discrete sets of styles.最适合离散样式集

    ```jsx
    // bad👎👎👎
    export default function MyComponent({ spacing }) {
      return (
        <div style={{ display: 'table', margin: spacing }} />
      );
    }

    // good👍👍👍
    function MyComponent({ styles, spacing }) {
      return (
        <div {...css(styles.periodic, { margin: spacing })} />
      );
    }
    export default withStyles(() => ({
      periodic: {
        display: 'table',
      },
    }))(MyComponent);
    ```

## Themes 主题

  - Use an abstraction layer使用一个抽象层 such as [react-with-styles](https://github.com/airbnb/react-with-styles) that enables theming.来允许主题 *react-with-styles gives us things like `withStyles()`, `ThemedStyleSheet`, and `css()` which are used in some of the examples in this document.*

  > Why? It is useful to have a set of shared variables for styling your components.一套共用的变量来样式化组件 Using an abstraction layer makes this more convenient.使用一个抽象层让它更方便使用 Additionally, this can help prevent your components from being tightly coupled to any particular underlying implementation, which gives you more freedom.另外，这会防止你的组件与任意特定的底层形成紧密的耦合，从而给你更高的自由度

  - Define colors only in themes.只在主题中定义颜色

    ```js
    // bad👎👎👎
    export default withStyles(() => ({
      chuckNorris: {
        color: '#bada55',
      },
    }))(MyComponent);

    // good👍👍👍
    export default withStyles(({ color }) => ({
      chuckNorris: {
        color: color.badass,
      },
    }))(MyComponent);
    ```

  - Define fonts only in themes.只在主题中定义字体

    ```js
    // bad👎👎👎
    export default withStyles(() => ({
      towerOfPisa: {
        fontStyle: 'italic',
      },
    }))(MyComponent);

    // good👍👍👍
    export default withStyles(({ font }) => ({
      towerOfPisa: {
        fontStyle: font.italic,
      },
    }))(MyComponent);
    ```

  - Define fonts as sets of related styles.把字体定义为相关样式集

    ```js
    // bad👎👎👎
    export default withStyles(() => ({
      towerOfPisa: {
        fontFamily: 'Italiana, "Times New Roman", serif',
        fontSize: '2em',
        fontStyle: 'italic',
        lineHeight: 1.5,
      },
    }))(MyComponent);

    // good👍👍👍
    export default withStyles(({ font }) => ({
      towerOfPisa: {
        ...font.italian,
      },
    }))(MyComponent);
    ```

  - Define base grid units in theme 在主题中定义基本网格单元 (either as a value or a function that takes a multiplier).

    ```js
    // bad👎👎👎
    export default withStyles(() => ({
      rip: {
        bottom: '-6912px', // 6 feet
      },
    }))(MyComponent);

    // good👍👍👍
    export default withStyles(({ units }) => ({
      rip: {
        bottom: units(864), // 6 feet, assuming our unit is 8px
      },
    }))(MyComponent);

    // good👍👍👍
    export default withStyles(({ unit }) => ({
      rip: {
        bottom: 864 * unit, // 6 feet, assuming our unit is 8px
      },
    }))(MyComponent);
    ```

  - Define media queries only in themes.在主题中定义

    ```js
    // bad👎👎👎
    export default withStyles(() => ({
      container: {
        width: '100%',

        '@media (max-width: 1047px)': {
          width: '50%',
        },
      },
    }))(MyComponent);

    // good👍👍👍
    export default withStyles(({ breakpoint }) => ({
      container: {
        width: '100%',

        [breakpoint.medium]: {
          width: '50%',
        },
      },
    }))(MyComponent);
    ```

  - Define tricky fallback properties in themes.定义主题中棘手的回退属性

    > Why? Many CSS-in-JavaScript implementations merge style objects together which makes specifying fallbacks for the same property很多CSS-in-JavaScript的实现合并样式对象让声明回退为新的属性 (e.g. `display`) a little tricky.有点棘手 To keep the approach unified, put these fallbacks in the theme.为保证方法统一，把这些回退属性放在主题里

    ```js
    // bad👎👎👎
    export default withStyles(() => ({
      .muscles {
        display: 'flex',
      },

      .muscles_fallback {
        'display ': 'table',
      },
    }))(MyComponent);

    // good👍👍👍
    export default withStyles(({ fallbacks }) => ({
      .muscles {
        display: 'flex',
      },

      .muscles_fallback {
        [fallbacks.display]: 'table',
      },
    }))(MyComponent);

    // good👍👍👍
    export default withStyles(({ fallback }) => ({
      .muscles {
        display: 'flex',
      },

      .muscles_fallback {
        [fallback('display')]: 'table',
      },
    }))(MyComponent);
    ```

  - Create as few custom themes as possible.创建尽可能少的自定义主题 Many applications may only have one theme.很多应用只有一个主题

  - Namespace custom theme settings under a nested object with a unique and descriptive key.命名空间自定义主题设定，具有唯一描述性键的嵌套对象

    ```js
    // bad👎👎👎
    ThemedStyleSheet.registerTheme('mySection', {
      mySectionPrimaryColor: 'green',
    });

    // good👍👍👍
    ThemedStyleSheet.registerTheme('mySection', {
      mySection: {
        primaryColor: 'green',
      },
    });
    ```

---

CSS puns adapted from [Saijo George](https://saijogeorge.com/css-puns/).
