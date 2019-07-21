```
  optimization: {
    splitChunks: {
      chunks: "all",
    }
  },
```

上面简单的一行代码就可以让webpack进行代码分割了，比如项目中引入了lodash（同步引入），这样webpack会自动的帮我们将代码进行分割。

就算是没有上面的配置，如果是异步代码引入的时候，比如：
```
async function getComponent() {
	const { default: _ } = await import(/* webpackChunkName:"lodash" */ 'lodash');
	const element = document.createElement('div');
	element.innerHTML = _.join(['Dell', 'Lee'], '-');
	return element;
}

document.addEventListener('click', () =>{
	getComponent().then(element => {
		document.body.appendChild(element);
	});
})
```
The same as below

```
function getComponent() {
	return import('lodash').then(({ default: _ }) => {
		var element = document.createElement('div');
		element.innerHTML = _.join(['Dell', 'Lee'], '-');
		return element;
	})
}

getComponent().then(element => {
	document.body.appendChild(element);
});
```

webpack 也是会帮我们做代码分割的，参见下一节：lesson-11


```
module.exports = {
  //...
  optimization: {
    splitChunks: {
      chunks: 'all', // 同时对同步和异步代码进行分割
      minSize: 30000,
      maxSize: 0,
      minChunks: 1,
      maxAsyncRequests: 5,
      maxInitialRequests: 3,
      automaticNameDelimiter: '~',
      automaticNameMaxLength: 30,
      name: true,
      cacheGroups: {
        vendors: {
          test: /[\\/]node_modules[\\/]/,
          priority: -10
        },
        default: {
          minChunks: 2,
          priority: -20,
          reuseExistingChunk: true
        }
      }
    }
  }
};
```
处理webpack默认的代码分割，上面的代码就是webpack具体对代码分割做的配置，也就是代码分割缓存组配置。
具体的配置看视频，这里只说思想。
缓存组，顾名思义，当要被分割的模块满足某一个组的要求的时候，这这个模块将会进入当前的组所生成的打包文件。
比如上面配置，webpack在进行代码分割的时候会同时配置基本配置和分组配置决定要被分割的模块是进入 vendors.js 还是 default.js