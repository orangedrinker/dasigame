# 求是 · 文军长征

仓库根目录的 **[final-game.html](final-game.html)** 是可独立运行的完整游戏，包含全部图片、音频、游戏逻辑及完整逐句歌词。下载原始文件后，用 Chrome 或 Edge 打开并点击开始即可；无需安装依赖或启动服务器。

## 校歌与歌词同步

- 使用用户确认的完整37句、153字歌词，保留最后重复的“天下来同”。
- Demucs分离人声，以中文CTC强制对齐并结合Whisper文本约束对齐、声学起音/尾音检查生成时间轴。
- 测得首次起唱为 **28.5秒**，作为全局 `introOffset`。前奏、间奏、完整原曲与尾声全部保留。
- 字幕、音符位置和判定共用 `AudioContext.currentTime`，计入播放起点和输出延迟；暂停和恢复共用同一时钟。
- 按每句的开始、结束时间显示当前歌词，句间空白隐藏；最后一句持续到166.5秒的尾音释放。
- 谱面按真实对齐字头重新生成，保留9.021秒的前奏音符。不会重复叠加前奏，也不会让末句超出约172.225秒的音频长度。

[lyrics-timeline.json](lyrics-timeline.json) 提供每句的起止时间和文本（单位：秒，保留0.1秒）；[lyrics-alignment-report.md](lyrics-alignment-report.md) 说明方法、声学复核及误差范围。时间戳分辨率不等于合唱所有软边界均保证±0.1秒。

## 验证

TypeScript检查、21项回归测试与生产构建通过。离线浏览器验证涵盖实际音频启动/起唱、暂停恢复、重开与退出、37句字幕的开始/结束边界、移动端显示；全部28项嵌入素材逐项SHA-256一致。

## 重新构建

`qiushi-improved(3).zip` 保存本次完整源码及构建结果。解压后在 `app/` 中运行：

```sh
npm ci
npm run check
npm test
npm run build
node scripts/build-single.mjs dist final-game.html
```

源码内 `src/game/anthem-phrases.json` 保存逐句与字符时间轴，`src/game/anthem-timing.ts` 定义 `introOffset` 和时间换算。`scripts/rebuild-aligned-chart.py` 可由已核验的字符对齐数据重新生成谱面。`lyric-sync-source.patch` 是本次相对于更新前源码包的差异；旧的 `rhythm-sync-source.patch` 留作历史参考。

主线程阻塞仍会延迟画面刷新和按键事件送达；恢复后位置按音频时钟更新，模型估计的合唱软边界也可能需要进一步听校。
