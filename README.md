# PlaneteiPosters

VRChat ワールド「ぷらね邸」が読み込む画像と一覧（GitHub Pages: https://mitake-h.github.io/PlaneteiPosters/ ）。
変更は push して数分でサイトに出る。ワールドは入ったときに読み込む（番組は 60 分、イベントは 30 分ごとにも読み直す）。

| ファイル | 使うところ |
|---|---|
| Poster1.jpg・Poster2.jpg | 入口のポスター |
| Signage1〜3.jpg | サイネージ 1・2 |
| Signage4・5.jpg | サイネージ 3・4（一覧にする前の画像。「元に戻す」で使う） |
| programs.json・programs/NN.jpg | サイネージ 3「自動投影番組」 |
| events.json | サイネージ 4「プラネタリウムイベント」 |

画像は 2048×2048 以下。サムネイルは 16:9（640×360 くらい）。

## programs.json（自動投影番組）

```json
{
  "programs": [
    {
      "slot": 3,
      "published": "2026-03-20",
      "title": "番組の題名",
      "role": "出演",
      "guide": "名前",
      "length": "90 分",
      "description": "説明（3 行ほどで切れる）"
    }
  ]
}
```

- 並び順は**新着順**: 再生できる番組を `published`（公開日）の新しい順に。公開日を書かない番組はそのあとに書いた順、`"slot": 0`（COMING SOON）は一番下に書いた順（最大 16 件）。
- `slot` は番組の枠の番号（1〜16）。枠ごとの動画の URL はワールドに入れてある（VRChat の決まりで、動画の URL はワールドに入れておく必要がある）。
  - 1 = ぷらね邸 1周年に寄せて、2 = 熟睡プラ寝たリウム in VRChat 2025。この 2 つは VRCDN の元の URL（`https://vrcdn.cloud/jumius/….mp4`）。
  - 3〜16 は VRCDN の別名 `https://vrcdn.cloud/static/jumius_program<N>`（予約）。
  - 0 は「COMING SOON」（再生ボタンなし）。
- **注意（2026-11-12）: VRCDN の別名（alias）は、10 MB より先から読む要求が失敗する**（VRCDN 側の不具合。元の URL は問題なし）。別名の動画は最初から通しで再生できるが、**シークすると Video Load Failed になる**。そのため枠 1・2 は元の URL にした。
- サムネイルは `programs/` に枠の番号で置く（枠 1 は `01.jpg`）。
- **新しい番組を足すとき**: どちらかで。
  - シークできるようにする（今のおすすめ）: VRCDN に動画を上げ、その元の URL を Unity の `PlaneteiSignageLists.cs` の `SlotVideoOverrides`（枠 N の場所）に書いて「サイネージ一覧/1」→ ワールドを上げ直す。ここに `"slot": N`・`"published"` の項目と `programs/NN.jpg` を足して push。
  - ワールドの更新なしで足す: VRCDN に動画を上げて `jumius_program<N>` の別名を付け、ここに項目とサムネイルを足して push（VRCDN の不具合が直るまでシークはできない）。
  - 例: 春の大曲線を公開するときは `"slot": 0` を `"slot": 3` に、`"published"` に公開日を書く（一番上に来る）。

## events.json（プラネタリウムイベント）

```json
{
  "events": [
    {
      "start": "2026-12-06 20:00",
      "end": "2026-12-06 21:30",
      "title": "イベントの題名",
      "host": "主催の名前",
      "description": "短い説明",
      "place": "ぷらね邸",
      "join": "グループに参加"
    }
  ]
}
```

- 時刻は日本時間。`24:00`・`25:30` のような書き方もできる。`end` は `"21:30"` のように時刻だけでもよい（開始と同じ日）。
- 開始の早い順に並び、`end` を過ぎると消える。`end` が無いときは開始から 2 時間で消える。時刻を書かない（日付だけの）ときはその日いっぱい出る。
- `host` の前の「主催」は `"hostLabel": "出演"` のように変えられる。`description`・`place`・`join` は書かなければ出ない。
- 最大 30 件。何も無いときは「いまは予定されているイベントはありません」と出る。
- `events_sample.json` は表示確認用のサンプル（題名に【サンプル】。過ぎたイベント・時刻だけの end・24:00・日付だけ・項目の省略・長い題名などの例）。
  確かめるときは中身を events.json に写して push し、終わったら本当のイベント（または `{"events": []}`）に戻す。

## 文字の注意

ワールドのフォント（Zen Maru Gothic）に無い字は表示されない。たとえば「―」（U+2015）は無いので、ダッシュは「—」（U+2014）を使う。
