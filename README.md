# 敏感数据识别性能提升实测：非结构化数据结果对比

## 行业现状
主流厂商均自研敏感数据识别引擎。从公开方案来看，针对非结构化数据的敏感数据识别，业界常规实现思路趋同。该方案在小体量数据场景下性能差异不明显，但在大数据量全量扫描时会出现明显性能衰减，进而带来算力资源浪费、扫描任务超时、巡检周期过长等问题。

## 测试说明
- **数据梯度**：1M～500M 文本数据，覆盖小规模功能验证到企业级大数据扫描场景
- **测试场景**：无匹配、微量匹配、适量匹配、全量匹配，覆盖日常业务工况与极限压力场景
- **对比基准**：传统方案 vs 优化方案

> 传统实现说明：文中传统实现为未做深度优化的基线版本，仅用于和优化 Demo 做对照，**不代表市面商用厂商的真实性能**。
>
> 验证说明：选取 15 条高频通用敏感规则作为验证集；仅评估非结构化文本识别性能；全部测试在单机单进程下执行，无集群、无硬件加速、无预处理裁剪，客观呈现优化前后的性能差距。

## 场景一：无匹配（无敏感数据）
该场景模拟纯非敏感文件，数据中不存在任何规则命中项，是业务里很常见的基线场景。传统方案平均性能约 11 MB/s，优化后可达约 107 MB/s，**性能提升约 10 倍**。

> 数据说明：全部为混淆数据，无任何敏感命中

<table>
  <thead>
    <tr>
      <th width="180" align="left" rowspan="2">指标</th>
      <th align="left" colspan="2">1M-0</th>
      <th align="left" colspan="2">10M-0</th>
      <th align="left" colspan="2">50M-0</th>
      <th align="left" colspan="2">100M-0</th>
      <th align="left" colspan="2">500M-0</th>
    </tr>
    <tr>
      <th align="left">传统</th>
      <th align="left">优化</th>
      <th align="left">传统</th>
      <th align="left">优化</th>
      <th align="left">传统</th>
      <th align="left">优化</th>
      <th align="left">传统</th>
      <th align="left">优化</th>
      <th align="left">传统</th>
      <th align="left">优化</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="left">总耗时（ms）</td>
      <td align="left">87.39</td>
      <td align="left">10.49</td>
      <td align="left">834.52</td>
      <td align="left">94.33</td>
      <td align="left">4260.69</td>
      <td align="left">442.89</td>
      <td align="left">8884.96</td>
      <td align="left">910.89</td>
      <td align="left">49775.07</td>
      <td align="left">4451.06</td>
    </tr>
    <tr>
      <td align="left">单规则平均耗时（ms）</td>
      <td align="left">5.83</td>
      <td align="left">0.70</td>
      <td align="left">55.63</td>
      <td align="left">6.29</td>
      <td align="left">284.05</td>
      <td align="left">29.53</td>
      <td align="left">592.33</td>
      <td align="left">60.73</td>
      <td align="left">3318.34</td>
      <td align="left">296.74</td>
    </tr>
    <tr>
      <td align="left">识别性能（MB/s）</td>
      <td align="left">11.44</td>
      <td align="left">95.33</td>
      <td align="left">11.98</td>
      <td align="left">106.01</td>
      <td align="left">11.74</td>
      <td align="left">112.89</td>
      <td align="left">11.25</td>
      <td align="left">109.78</td>
      <td align="left">10.06</td>
      <td align="left">112.33</td>
    </tr>
    <tr>
      <td align="left">1T数据识别耗时（小时）</td>
      <td align="left">25.46</td>
      <td align="left">3.06</td>
      <td align="left">24.31</td>
      <td align="left">2.75</td>
      <td align="left">24.81</td>
      <td align="left">2.58</td>
      <td align="left">25.89</td>
      <td align="left">2.65</td>
      <td align="left">28.95</td>
      <td align="left">2.59</td>
    </tr>
  </tbody>
</table>

## 场景二：微量匹配（每条规则命中2条）
敏感样本集中放置在数据尾部，前置主体全部为混淆数据，模拟海量文件里仅零星命中敏感内容的工况。传统方案平均性能约 9 MB/s，优化后约 119 MB/s，**性能提升约 12 倍**。

> 数据说明：前置混淆数据，每条规则仅命中约2条

<table>
  <thead>
    <tr>
      <th width="180" align="left" rowspan="2">指标</th>
      <th align="left" colspan="2">1M-2</th>
      <th align="left" colspan="2">10M-2</th>
      <th align="left" colspan="2">50M-2</th>
      <th align="left" colspan="2">100M-2</th>
      <th align="left" colspan="2">500M-2</th>
    </tr>
    <tr>
      <th align="left">传统</th>
      <th align="left">优化</th>
      <th align="left">传统</th>
      <th align="left">优化</th>
      <th align="left">传统</th>
      <th align="left">优化</th>
      <th align="left">传统</th>
      <th align="left">优化</th>
      <th align="left">传统</th>
      <th align="left">优化</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="left">总耗时（ms）</td>
      <td align="left">93.77</td>
      <td align="left">9.73</td>
      <td align="left">1015.86</td>
      <td align="left">77.31</td>
      <td align="left">5267.83</td>
      <td align="left">384.11</td>
      <td align="left">10453.04</td>
      <td align="left">780.52</td>
      <td align="left">60885.88</td>
      <td align="left">4677.82</td>
    </tr>
    <tr>
      <td align="left">单规则平均耗时（ms）</td>
      <td align="left">6.25</td>
      <td align="left">0.65</td>
      <td align="left">67.72</td>
      <td align="left">5.15</td>
      <td align="left">351.19</td>
      <td align="left">25.61</td>
      <td align="left">696.87</td>
      <td align="left">52.03</td>
      <td align="left">4059.06</td>
      <td align="left">311.85</td>
    </tr>
    <tr>
      <td align="left">识别性能（MB/s）</td>
      <td align="left">10.66</td>
      <td align="left">102.77</td>
      <td align="left">9.84</td>
      <td align="left">129.35</td>
      <td align="left">9.49</td>
      <td align="left">130.17</td>
      <td align="left">9.57</td>
      <td align="left">128.12</td>
      <td align="left">8.21</td>
      <td align="left">106.89</td>
    </tr>
    <tr>
      <td align="left">1T数据识别耗时（小时）</td>
      <td align="left">27.32</td>
      <td align="left">2.83</td>
      <td align="left">29.60</td>
      <td align="left">2.25</td>
      <td align="left">30.69</td>
      <td align="left">2.24</td>
      <td align="left">30.44</td>
      <td align="left">2.27</td>
      <td align="left">35.48</td>
      <td align="left">2.72</td>
    </tr>
  </tbody>
</table>

## 场景三：适量匹配（敏感:混淆 ≈ 1:1000）
敏感数据与混淆数据按 1:1000 比例混合，模拟业务中存在一定量敏感内容的工况，匹配量介于微量匹配与全量匹配之间。传统方案平均性能约 9 MB/s，优化后约 104 MB/s，**性能提升约 11 倍**。

> 数据说明：敏感数据和混淆数据按 1:1000 混合，重复生成对应大小数据

<table>
  <thead>
    <tr>
      <th width="180" align="left" rowspan="2">指标</th>
      <th align="left" colspan="2">1M-30</th>
      <th align="left" colspan="2">10M-286</th>
      <th align="left" colspan="2">50M-1422</th>
      <th align="left" colspan="2">100M-2842</th>
      <th align="left" colspan="2">500M-128962</th>
    </tr>
    <tr>
      <th align="left">传统</th>
      <th align="left">优化</th>
      <th align="left">传统</th>
      <th align="left">优化</th>
      <th align="left">传统</th>
      <th align="left">优化</th>
      <th align="left">传统</th>
      <th align="left">优化</th>
      <th align="left">传统</th>
      <th align="left">优化</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="left">总耗时（ms）</td>
      <td align="left">113.41</td>
      <td align="left">10.98</td>
      <td align="left">1086.22</td>
      <td align="left">93.44</td>
      <td align="left">5129.71</td>
      <td align="left">450.33</td>
      <td align="left">10148.01</td>
      <td align="left">932.15</td>
      <td align="left">63499.24</td>
      <td align="left">4675.99</td>
    </tr>
    <tr>
      <td align="left">单规则平均耗时（ms）</td>
      <td align="left">7.56</td>
      <td align="left">0.73</td>
      <td align="left">72.41</td>
      <td align="left">6.23</td>
      <td align="left">341.98</td>
      <td align="left">30.02</td>
      <td align="left">676.53</td>
      <td align="left">62.14</td>
      <td align="left">4233.28</td>
      <td align="left">311.73</td>
    </tr>
    <tr>
      <td align="left">识别性能（MB/s）</td>
      <td align="left">8.82</td>
      <td align="left">91.07</td>
      <td align="left">9.21</td>
      <td align="left">107.02</td>
      <td align="left">9.75</td>
      <td align="left">111.03</td>
      <td align="left">9.85</td>
      <td align="left">107.28</td>
      <td align="left">7.87</td>
      <td align="left">106.93</td>
    </tr>
    <tr>
      <td align="left">1T数据识别耗时（小时）</td>
      <td align="left">33.02</td>
      <td align="left">3.20</td>
      <td align="left">31.63</td>
      <td align="left">2.72</td>
      <td align="left">29.87</td>
      <td align="left">2.62</td>
      <td align="left">29.57</td>
      <td align="left">2.72</td>
      <td align="left">37.01</td>
      <td align="left">2.72</td>
    </tr>
  </tbody>
</table>

## 场景四：全量匹配（极限满匹配）
该场景模拟单条记录包含全部识别项的极限工况（真实业务出现概率极低）。传统方案平均性能约 6 MB/s，优化后约 41 MB/s，**性能提升约 7 倍**。

> 数据说明：满匹配，一条记录包含所有识别项，重复生成对应大小数据

<table>
  <thead>
    <tr>
      <th width="180" align="left" rowspan="2">指标</th>
      <th align="left" colspan="2">1M-1971</th>
      <th align="left" colspan="2">10M-19711</th>
      <th align="left" colspan="2">50M-98551</th>
      <th align="left" colspan="2">100M-197101</th>
      <th align="left" colspan="2">500M-985504</th>
    </tr>
    <tr>
      <th align="left">传统</th>
      <th align="left">优化</th>
      <th align="left">传统</th>
      <th align="left">优化</th>
      <th align="left">传统</th>
      <th align="left">优化</th>
      <th align="left">传统</th>
      <th align="left">优化</th>
      <th align="left">传统</th>
      <th align="left">优化</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="left">总耗时（ms）</td>
      <td align="left">173.17</td>
      <td align="left">24.04</td>
      <td align="left">1481.22</td>
      <td align="left">245.44</td>
      <td align="left">7830.61</td>
      <td align="left">1197.85</td>
      <td align="left">14575.74</td>
      <td align="left">2620.14</td>
      <td align="left">128717.2</td>
      <td align="left">12681.3</td>
    </tr>
    <tr>
      <td align="left">单规则平均耗时（ms）</td>
      <td align="left">11.54</td>
      <td align="left">1.60</td>
      <td align="left">98.75</td>
      <td align="left">16.36</td>
      <td align="left">522.04</td>
      <td align="left">79.86</td>
      <td align="left">971.72</td>
      <td align="left">174.68</td>
      <td align="left">8581.15</td>
      <td align="left">845.42</td>
    </tr>
    <tr>
      <td align="left">识别性能（MB/s）</td>
      <td align="left">5.77</td>
      <td align="left">41.60</td>
      <td align="left">6.75</td>
      <td align="left">40.74</td>
      <td align="left">6.39</td>
      <td align="left">41.74</td>
      <td align="left">6.86</td>
      <td align="left">38.17</td>
      <td align="left">3.88</td>
      <td align="left">39.43</td>
    </tr>
    <tr>
      <td align="left">1T数据识别耗时（小时）</td>
      <td align="left">50.48</td>
      <td align="left">7.00</td>
      <td align="left">43.15</td>
      <td align="left">7.15</td>
      <td align="left">45.58</td>
      <td align="left">6.98</td>
      <td align="left">42.46</td>
      <td align="left">7.63</td>
      <td align="left">75.07</td>
      <td align="left">7.39</td>
    </tr>
  </tbody>
</table>

## 技术核心总结
从 Demo 验证结果来看，绝大多数业务场景下性能提升接近 10 倍（全量匹配属于极低概率极限场景）。传统串行逐条匹配方案在大数据扫描场景下存在显著优化空间，基线方案的低效会直接带来三大痛点：巡检周期长、算力资源浪费、风险发现滞后。

以大型企业每日处理 1 PB 数据为例，按本次测试性能估算，每月可节省算力成本 3 万元以上。

> 成本测算假设：单进程规格 1c1g，30 元/月，折合 1 元/天

<table>
  <thead>
    <tr>
      <th width="180" align="left">方案</th>
      <th align="left">数据量(MB)</th>
      <th align="left">平均性能(MB/s)</th>
      <th align="left">单进程处理时长(天)</th>
      <th align="left">所需并发数</th>
      <th align="left">日成本(元)</th>
      <th align="left">月成本(元)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="left">传统</td>
      <td align="left">1073741824</td>
      <td align="left">8.97</td>
      <td align="left">1385.46</td>
      <td align="left">1386</td>
      <td align="left">1386</td>
      <td align="left">41580</td>
    </tr>
    <tr>
      <td align="left">优化</td>
      <td align="left">1073741824</td>
      <td align="left">93.10</td>
      <td align="left">133.49</td>
      <td align="left">134</td>
      <td align="left">134</td>
      <td align="left">4020</td>
    </tr>
  </tbody>
</table>

## 技术交流
不同厂商的规则集、数据样本、底层实现存在差异，测试结果会有区别。本次验证说明，敏感数据识别引擎仍有很大底层优化空间。欢迎数据安全同行、架构师交流。

> free.monster(free.monster@icloud.com)，专注数据安全底层技术自研与实战落地

