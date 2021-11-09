# 今日の積み上げ
- 【Web】chapter3クリア。最低限のレベルで理解しておけばいいかと。そんなに完璧な理解はいらないかな。
- 【現場】chapter2までクリア。明日からアプリを作りながら学習。(MVCについて再確認)
- 【paiza】Bランクに挑戦するも玉砕。解答例があるからコードを紐解いていけば、進めるかなと。 

```
Bランクの問題の解答例
$board = []

# 盤面の初期化
(1..5).each { $board.push(gets.chomp.split('')) }

def aligned?(o, x)
  if o == 5
    'O'
  elsif x == 5
    'X'
  else
    'D'
  end
end

def row_aligned?
  result = ''

  $board.each do |row|
    o = 0
    x = 0
    (0..4).each do |i|
      if row[i] == 'O'
        o = o + 1
      elsif row[i] == 'X'
        x = x + 1
      else
        break
      end
    end

    result = aligned?(o, x)
    break if result != 'D'
  end

  result
end

def collum_aligned?
  result = ''

  (0..4).each do |i|
    o = 0
    x = 0
    $board.each do |row|
      if row[i] == 'O'
        o = o + 1
      elsif row[i] == 'X'
        x = x + 1
      end
    end

    result = aligned?(o, x)
    break if result != 'D'
  end

  result
end

def oblique_aligned?
  result = ''

  (0..1).each do |time|
    i = 0

    if time.zero?
      j = 0
    else
      j = 4
    end

    o = 0
    x = 0
    (1..5).each do
      if $board[i][j] == 'O'
        o = o + 1
      elsif $board[i][j] == 'X'
        x = x + 1
      end

      i = i + 1

      if time.zero?
        j = j + 1
      else
        j = j - 1
      end
    end

    result = aligned?(o, x)
    break if result != 'D'
  end

  result
end

if row_aligned? == 'O' || collum_aligned? == 'O' || oblique_aligned? == 'O'
  puts 'O'
elsif row_aligned? == 'X' || collum_aligned? == 'X' || oblique_aligned? == 'X'
  puts 'X'
else
  puts 'D'
end
```
# 明日のTODO
- 【Web】chapter4
- 【現場】chapter3
- 【paiza】問題集。五目並べのコードを分析と占いをクリアする。
# コメント
これで成長しているのか不安。
