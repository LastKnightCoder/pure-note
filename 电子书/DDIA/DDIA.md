开始日期：2022-08-01

```antd
const { Progress } = antd
const datas = [{
  name: 'All',
  percent: 552/552
}, {
  name: 'Chapter 1',
  percent: 24/24
}, {
  name: 'Chapter 2',
  percent: 42/42
}, {
  name: 'Chapter 3',
  percent: 42/42
}, {
  name: 'Chapter 4',
  percent: 32/32
}, {
  name: 'Chapter 5',
  percent: 48/48
}, {
  name: 'Chapter 6',
  percent: 22/22
}, {
  name: 'Chapter 7',
  percent: 52/52
}, {
  name: 'Chapter 8',
  percent: 48/48
}, {
  name: 'Chapter 9',
  percent: 62/62
}, {
  name: 'Chapter 10',
  percent: 50/50
}, {
  name: 'Chapter 11',
  percent: 50/50
}, {
  name: 'Chapter 12',
  percent: 64/64
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

结束日期：2022-08-22