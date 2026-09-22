# CSV DownLoad Sample Code

```ts
/**
 * ファイルダウンロード処理
 *
 * Axiosのレスポンス（Blob）からファイルを生成し、ブラザでダウンロードを実行する
 * Content-Dispositionヘッダからファイル名を取得し、指定されていればその名前で保存する。
 *
 * @param response
 */
export function downloadFile(response: AxiosResponse<Blob>) {
  // バックエンドから受け取ったファイルデータ
  const blob = response.data;

  // HTTPレスポンスヘッダのContent-Dispositionを取得
  const disposition = response.headers["content-disposition"];

  let fileName: string | undefined;

  // Content-Dispositionにfilenameが含まれている場合はファイル名を取得
  if (disposition && disposition.includes("filename=")) {
    const match = disposition.match(/filename\*?=(?:UTF-8'')?"?([^";]+)/);

    // ファイル名を設定
    if (match?.[1]) fileName = decodeURI(match[1]);
  }

  // Blobから一時的なURLを生成
  const url = window.URL.createObjectURL(blob);
  // ダウンロード用のaタグを作成
  const a = document.createElement("a");

  a.href = url;
  // ファイル名が取得できた場合のみダウンロード名を設定
  if (fileName) {
    a.download = fileName;
  }
  // 自動クリックでダウンロード開始
  a.click();
  // 作成したURLを解放
  window.URL.revokeObjectURL(url);
}
```
