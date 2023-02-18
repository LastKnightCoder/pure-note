开始日期：2022-06-08

```antd
const { Progress } = antd
const datas = [{
  name: 'All',
  percent: 1075/1075
}, {
  name: 'Chapter 1',
  percent: 28/28
}, {
  name: 'Chapter 2',
  percent: 131/131
}, {
  name: 'Chapter 3',
  percent: 188/188
}, {
  name: 'Chapter 4',
  percent: 144/144
}, {
  name: 'Chapter 5',
  percent: 84/84
}, {
  name: 'Chapter 6',
  percent: 88/88
}, {
  name: 'Chapter 7',
  percent: 51/51
}, {
  name: 'Chapter 8',
  percent: 79/79
}, {
  name: 'Chapter 9',
  percent: 85/85
}, {
  name: 'Chapter 10',
  percent: 28/28
}, {
  name: 'Chapter 11',
  percent: 53/53
}, {
  name: 'Chapter 12',
  percent: 69/69
}]

const NamedProgress = props => {
  return (
    <div style={{display: 'flex', gap: 10, alignItems: 'center', margin: '10px 0', maxWidth: '600px'}}>
      <div style={{ whiteSpace: 'nowrap', fontSize: '14px' }}>
        {props.name + ': '}
      </div>
      <Progress 
        strokeColor={{ from: '#108ee9', to: '#87d068' }} 
        percent={props.percent * 100} 
        status="active"
        format={percent => Math.floor(percent * 100) / 100 + '%'}
      />
    </div>
  )
}

const root = ReactDOM.createRoot(el)
root.render(<>
  {datas.map(data => <NamedProgress key={data.name} {...data} />)}
</>)
```

结束日期：2022-07-31