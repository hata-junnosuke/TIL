# Dirクラスメソッド
以下は Dir クラスの主なクラスメソッドの一覧です。
- Dir.chdir(dir): 指定されたディレクトリにカレントディレクトリを変更します。
- Dir.chdir(dir) { block }: ブロック内で指定されたディレクトリにカレントディレクトリを変更し、ブロックを実行します。
- Dir.delete(dir): 指定されたディレクトリを削除します。
- Dir.entries(dir, encoding: nil): 指定されたディレクトリ内のエントリ（ファイルやサブディレクトリ）のリストを返します。
- Dir.exist?(dir): 指定されたディレクトリが存在するかどうかを判定します。
- Dir.exists?(dir) (非推奨): Dir.exist? の別名です。
- Dir.foreach(dir, encoding: nil) { |filename| block }: 指定されたディレクトリ内の各エントリに対してブロックを実行します。
- Dir.getwd: カレントディレクトリのパスを返します。
- Dir.glob(pattern, base: nil, sort: true): 指定されたパターンに一致するファイルのリストを返します。
- Dir.mkdir(dir, permission: 0755): 指定されたディレクトリを作成します。
- Dir.new(dir): 指定されたディレクトリを表す Dir オブジェクトを返します。
- Dir.open(dir, encoding: nil) { |dir| block }: 指定されたディレクトリを開き、ブロック内でディレクトリに対する操作を行います。
- Dir.pwd: カレントディレクトリのパスを返します。Dir.getwd のエイリアスです。
- Dir.rmdir(dir): 指定されたディレクトリを削除します。

# Fileクラス
- File.atime(file): 指定されたファイルの最終アクセス時刻を返します。
- File.basename(file, suffix = ''): 指定されたパスのベース名を返します。
- File.birthtime(file): 指定されたファイルの作成時刻を返します。
- File.ctime(file): 指定されたファイルの最終状態変更時刻を返します。
- File.delete(*files): 指定されたファイルを削除します。
- File.directory?(file): 指定されたパスがディレクトリかどうかを判定します。
- File.exist?(file): 指定されたファイルが存在するかどうかを判定します。
- File.exists?(file) (非推奨): File.exist? の別名です。
- File.extname(file): 指定されたパスの拡張子を返します。
- File.file?(file): 指定されたパスがファイルかどうかを判定します。
- File.fnmatch?(pattern, path, flags = 0): 指定されたパスがパターンに一致するかどうかを判定します。
- File.join(*parts): 指定されたパスの部分を結合して新しいパスを作成します。
- File.mtime(file): 指定されたファイルの最終更新時刻を返します。
- File.new(file[, mode[, perm]]): 指定されたファイルを表す File オブジェクトを返します。
- File.open(file[, mode]): 指定されたファイルを開き、ブロック内でファイルに対する操作を行います。
- File.read(file, length = nil, offset = nil, mode: nil): 指定されたファイルを読み込み、その内容を返します。
- File.rename(old_name, new_name): 指定されたファイルの名前を変更します。
- File.size(file): 指定されたファイルのサイズ（バイト単位）を返します。
- File.stat(file): 指定されたファイルの詳細な情報を返します。
- File.unlink(file): 指定されたファイルを削除します。

# IOクラス
- IO.binread(file, length = nil, offset = nil): 指定されたファイルをバイナリ形式で読み込み、その内容を返します。
- IO.binwrite(file, string, offset = nil): 指定されたファイルにバイナリ形式でデータを書き込みます。
- IO.copy_stream(src, dst, copy_length = nil, src_offset = nil, dst_offset = nil): ソースファイルからデータを読み取り、デスティネーションファイルに書き込みます。
- IO.foreach(file, sep = $/, &block): 指定されたファイルを行ごとに読み込み、各行に対してブロックを実行します。
- IO.new(fd, mode: 'r'[, options]): 指定されたファイルディスクリプタを表す IO オブジェクトを返します。
- IO.open(file[, mode[, perm]]): 指定されたファイルを開き、ブロック内でファイルに対する操作を行います。
- IO.popen(cmd, mode = 'r'[, options]): 指定されたコマンドを実行し、その入力または出力としてパイプを開きます。
- IO.read(file, length = nil, offset = nil, mode: nil): 指定されたファイルを読み込み、その内容を返します。
- IO.readlines(file, sep = $/): 指定されたファイルを行ごとに読み込み、行の配列を返します。
- IO.select(read_array[, write_array[, error_array[, timeout]]]): 指定されたIOオブジェクトの読み込み/書き込み/エラー状態を監視します。
- IO.sysopen(file[, mode[, perm]]): 指定されたファイルを開き、ファイルディスクリプタを返します。
- IO.write(file, string, offset = nil): 指定されたファイルにデータを書き込みます。
