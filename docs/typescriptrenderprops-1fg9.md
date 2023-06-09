# TypeScriptでRenderPropsをやった

> 原文：<https://dev.to/ozwsei/typescriptrenderprops-1fg9>

[React 在 TypeScript 中渲染道具——詹姆斯·拉文斯克罗夫特——Medium](https://medium.com/@jrwebdev/react-render-props-in-typescript-b561b00bc67c)

[渲染道具-反应](https://reactjs.org/docs/render-props.html)

```
import { storiesOf } from '@storybook/react';
import * as React from 'react';

interface NumberHolderInjectionProps {
  num?: number;
  onChange(num: number): void;
}

interface NumberHolderProps {
  children(injection: NumberHolderInjectionProps): JSX.Element;
}

interface NumberHolderState {
  num?: Number;
}

class NumberHolder extends React.Component<NumberHolderProps, NumberHolderState> {
  state = {
    num: undefined
  };

  onChange = (num: number) => {
    this.setState({
      num
    });
  }

  render(): JSX.Element {
    return this.props.children({
      num: this.state.num,
      onChange: this.onChange
    });
  }
}

interface NumberRendererProps extends NumberHolderInjectionProps {}

let isFirst = true;

const NumberRenderer: React.SFC<NumberRendererProps> = (props) => {
    // コレがないと無限ループする
  if (isFirst) {
    props.onChange(777);
    isFirst = false;
  }

  return (
    <div>
      <p>{ (props.num || 10000) } </p>
    </div>
  );
};

const WrappedNumberRenderer: React.SFC<{}> = (_) => (
  <NumberHolder>
    {(injection: NumberHolderInjectionProps) => <NumberRenderer {...injection} />}
  </NumberHolder>
);

storiesOf('TypeScript＆RenderPropsの実験実装', module)
  .add('Simple 実装', () => (
    <WrappedNumberRenderer />
  )); 
```