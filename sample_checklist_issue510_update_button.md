# PR チェックリスト: #510 受注編集に更新ボタンを追加

| 項目 | 内容 |
|---|---|
| Issue | #510 受注編集に更新ボタンを追加 |
| マージ先 (base) | `origin/master` (6b8e09f) |
| feature ブランチ | `origin/feature/ISSUE-510-order-update` (b9add4f) |
| マージベース | 6b8e09f8bf |
| コミット数 | 1 |
| 変更ファイル | 4 ファイル (+82 / -0) |
| 生成日時 | 2026-10-16 13:21 |
| AI モデル | 未使用(ルールベースのみ) |

**チェック項目: 40 件** ― 🔴 高 12 / 🟡 中 22 / 🟢 低 6

## 1. 共通チェック

- [ ] 🔴 **[高]** Issue の要件・受け入れ条件をすべて満たしているか
  - 要件 ― PR の目的を満たしていることを最初に確認するため。
- [ ] 🔴 **[高]** dotnet build / 既存の単体テストがエラー・新規警告なしで通るか
  - ビルド ― C# の変更が含まれるため。
- [ ] 🔴 **[高]** ng build / ng lint / ng test がエラーなしで通るか
  - ビルド ― Angular の変更が含まれるため。
- [ ] 🟡 **[中]** マージ先ブランチの最新を取り込み、コンフリクトがないか
  - リリース ― マージ時の衝突・デグレを防ぐため。
- [ ] 🟡 **[中]** 対象ブラウザ・画面サイズで表示崩れがないか、変更画面のスクリーンショットを PR に添付したか
  - UI ― 画面の変更が含まれるため。
- [ ] 🟡 **[中]** API の入出力(DTO の項目名・型・必須)がフロントとバックエンドで一致しているか
  - 影響範囲 ― C# と Angular の両方に変更があるため。
- [ ] 🟡 **[中]** テストコードの追加・更新が不要である理由が説明されているか
  - テスト ― 差分にテストコードが含まれていないため。
- [ ] 🟢 **[低]** PR の説明に変更内容・影響範囲・テスト結果・関連 Issue 番号が記載されているか
  - レビュー ― レビュアーと後から見る人が変更意図を追えるようにするため。

## 2. 動作確認(操作ごとの更新前チェック・更新後の表示)

差分に含まれる操作(ボタンなど)ごとに、実行前のチェック条件と実行後の画面の変化をまとめています。

### 「更新」ボタン

`order-edit.component.html L13 → update() → PUT /api/orders/${this.order.id} → Update()`

**更新前チェック(条件を満たさないときは更新されないこと)**

- [ ] 🟡 **[中]** `form.get('quantity')?.hasError('required')` のとき『数量を入力してください』が表示され、更新できないこと (order-edit.component.html L6)
  - 根拠: `@if (form.get('quantity')?.hasError('required')) {`
- [ ] 🟡 **[中]** `form.get('quantity')?.hasError('min')` のとき『数量は1以上を入力してください』が表示され、更新できないこと (order-edit.component.html L9)
  - 根拠: `<span class="error" *ngIf="form.get('quantity')?.hasError('min')">数量は1以上を入力してください</span>`
- [ ] 🟡 **[中]** `form.invalid || saving` のとき「更新」ボタンが押せないこと (order-edit.component.html L13)
  - 根拠: `<button type="button" (click)="update()" [disabled]="form.invalid || saving">更新</button>`
- [ ] 🔴 **[高]** 「quantity」が未入力のとき、エラーになり更新できないこと (order-edit.component.ts L10)
  - 根拠: `quantity: [null, [Validators.required, Validators.min(1), Validators.max(9999)]],`
- [ ] 🔴 **[高]** 「quantity」が 1 未満のときエラー、1 ちょうどで更新できること(境界値) (order-edit.component.ts L10)
  - 根拠: `quantity: [null, [Validators.required, Validators.min(1), Validators.max(9999)]],`
- [ ] 🟡 **[中]** 「quantity」が 9999 を超えるときエラー、9999 ちょうどで更新できること(境界値) (order-edit.component.ts L10)
  - 根拠: `quantity: [null, [Validators.required, Validators.min(1), Validators.max(9999)]],`
- [ ] 🟡 **[中]** 「note」が 200 文字を超えるときエラー、200 文字ちょうどで更新できること(境界値) (order-edit.component.ts L11)
  - 根拠: `note: ['', Validators.maxLength(200)],`
- [ ] 🔴 **[高]** `this.order.status === 'S'` のとき『出荷済みの受注は更新できません』が表示され、更新されないこと (order-edit.component.ts L15)
  - 根拠: `if (this.order.status === 'S') {`
- [ ] 🟡 **[中]** 確認ダイアログ『受注を更新します。よろしいですか？』が表示され、キャンセルした場合は更新されないこと (order-edit.component.ts L19)
  - 根拠: `if (!confirm('受注を更新します。よろしいですか？')) {`
- [ ] 🟡 **[中]** 処理中は再度押せない(二重に更新されない)こと (order-edit.component.ts L22)
  - 根拠: `this.saving = true;`
- [ ] 🔴 **[高]** サーバー側: `dto.Quantity <= 0` のとき『数量は1以上を指定してください』でエラーになり、更新されないこと (OrderController.cs L17)
  - 根拠: `if (dto.Quantity <= 0) return BadRequest("数量は1以上を指定してください");`
- [ ] 🔴 **[高]** サーバー側: `order == null` のとき NotFound になり、更新されないこと (OrderController.cs L19)
  - 根拠: `if (order == null) return NotFound();`
- [ ] 🔴 **[高]** サーバー側: `order.Status == "S"` のとき『出荷済みの受注は更新できません』でエラーになり、更新されないこと (OrderController.cs L20)
  - 根拠: `if (order.Status == "S") throw new BusinessException("出荷済みの受注は更新できません");`
- [ ] 🔴 **[高]** 他のユーザーが先に更新していた場合、上書きされずに排他エラーになること(『他のユーザーが更新しています。再読込してください』) (OrderController.cs L21)
  - 根拠: `if (order.RowVersion != dto.RowVersion)`
- [ ] 🔴 **[高]** サーバー側: 「Quantity」が未指定のとき、エラーで更新されないこと(メッセージ『数量は必須です』) (OrderUpdateDto.cs L7)
  - 根拠: `[Required(ErrorMessage = "数量は必須です")]`
- [ ] 🔴 **[高]** サーバー側: 「Quantity」が 1〜9999 の範囲外のときエラー、境界値 1 / 9999 では更新できること(メッセージ『数量は1〜9999で入力してください』) (OrderUpdateDto.cs L8)
  - 根拠: `[Range(1, 9999, ErrorMessage = "数量は1〜9999で入力してください")]`
- [ ] 🟡 **[中]** サーバー側: 「Note」が 200 文字を超えるときエラーになること (OrderUpdateDto.cs L11)
  - 根拠: `[StringLength(200)]`

**更新後の表示(正常に更新されたとき)**

- [ ] 🟡 **[中]** 更新後に『更新しました』が表示されること (order-edit.component.ts L25)
  - 根拠: `this.message.success('更新しました');`
- [ ] 🟡 **[中]** 更新後に `order` の内容が画面に反映されること (order-edit.component.ts L26)
  - 根拠: `this.order = res;`
- [ ] 🟡 **[中]** 更新後に再読込(`this.loadHistory()`)され、最新の内容が表示されること (order-edit.component.ts L27)
  - 根拠: `this.loadHistory();`
- [ ] 🟢 **[低]** 更新後に入力内容がリセット(未変更の状態)になること (order-edit.component.ts L28)
  - 根拠: `this.form.markAsPristine();`
- [ ] 🟢 **[低]** 更新完了後(成功・失敗とも)に処理中の表示が解除され、再度操作できること (order-edit.component.ts L29)
  - 根拠: `this.saving = false;`
- [ ] 🟡 **[中]** 更新後に `UpdatedAt` が記録され、画面に表示される場合は最新の値になること (OrderController.cs L24)
  - 根拠: `order.UpdatedAt = DateTime.Now;`
- [ ] 🟡 **[中]** 更新後に `UpdatedBy` が記録され、画面に表示される場合は最新の値になること (OrderController.cs L25)
  - 根拠: `order.UpdatedBy = _user.Name;`

**表示の変更**

- [ ] 🟢 **[低]** 「最終更新:」が書式 date('yyyy/MM/dd HH:mm')で表示されること(値が空のときの表示も確認) (order-edit.component.html L11)
  - 根拠: `<p>最終更新: {{ order.updatedAt | date:'yyyy/MM/dd HH:mm' }}</p>`
- [ ] 🟢 **[低]** `order.status === 'C'` のとき見た目(`cancelled`)が切り替わること (order-edit.component.html L12)
  - 根拠: `<span class="status" [class.cancelled]="order.status === 'C'">{{ statusLabel(order.status…`

**更新失敗時**

- [ ] 🟡 **[中]** 更新に失敗したとき『更新に失敗しました』が表示されること (order-edit.component.ts L32)
  - 根拠: `this.message.error(e.error?.message ?? '更新に失敗しました');`

## 3. ファイル別チェック

### `src/Erp.Api/Controllers/OrderController.cs`

C# / 変更 / +16 -0

- [ ] 🟡 **[中]** 追加/変更した API に認可([Authorize]/権限チェック)と入力検証があるか (L14)
  - セキュリティ ― 権限のないユーザーによる参照・更新を防ぐため。
- [ ] 🟢 **[低]** DateTime.Now/Today の使用がタイムゾーン・締め日・テスト容易性の観点で問題ないか (L24)
  - 業務ロジック ― サーバーのタイムゾーン依存や日付境界の不具合が起きやすいため。

### `web/src/app/order/order-edit.component.html`

HTML / 変更 / +11 -0

- [ ] 🟡 **[中]** 入力項目にラベル・必須/桁数/形式のバリデーションとエラーメッセージがあるか (L5, L10)
  - 入力検証 ― 不正値の登録や、ユーザーが入力誤りに気付けない状態を防ぐため。

### `web/src/app/order/order-edit.component.ts`

TypeScript / 変更 / +39 -0

- [ ] 🟡 **[中]** subscribe の購読解除(takeUntilDestroyed / async パイプ等)ができているか (L23)
  - 信頼性 ― 画面遷移後も購読が残り、メモリリークや二重処理の原因になるため。
- [ ] 🟡 **[中]** HTTP 呼び出しのエラー処理(catchError・エラーメッセージ表示)があるか (L23)
  - エラー処理 ― 通信エラー時に画面が固まる・無反応になるのを防ぐため。

## 変更ファイル一覧

| # | ファイル | 種別 | 状態 | 追加 | 削除 | 項目数 |
|---|---|---|---|---:|---:|---:|
| 1 | `src/Erp.Api/Controllers/OrderController.cs` | C# | 変更 | 16 | 0 | 2 |
| 2 | `src/Erp.Api/Dtos/OrderUpdateDto.cs` | C# | 追加 | 16 | 0 | 0 |
| 3 | `web/src/app/order/order-edit.component.html` | HTML | 変更 | 11 | 0 | 1 |
| 4 | `web/src/app/order/order-edit.component.ts` | TypeScript | 変更 | 39 | 0 | 2 |

## コミット

- `b9add4f` #510 受注編集に更新ボタンを追加 (tester, 2026-10-16)

---

_このチェックリストは開発者サポートAIが自動生成しました。`AI` 付きの項目は社内 LLM による提案のため、内容を確認のうえ不要なら削除してください。_
