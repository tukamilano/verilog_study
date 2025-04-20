verilog_manual
================

[verilog 簡易マニュアル](https://exp.mtl.t.u-tokyo.ac.jp/2024/b3exp/-/wikis/VerilogHDLManual)の中の一部を解説します.

``` verilog
module and_gate (
    input wire inA,  // 1bitの入力信号
    input wire inB,  // 1bitの入力信号
    output wire out  // 1bitの出力信号
);
    assign out = inA & inB;  // AND演算の組み合わせ回路
endmodule
```

``` verilog
module testbench;
    // パラメータ
    parameter CYCLE = 1000;
    parameter HALF_CYCLE = 500;
    parameter DLY = 500;

    // 信号の定義
    reg clk;
    reg inA, inB;
    wire out_and_gate;

    // テスト対象モジュールのANDゲートをインスタンス化
    and_gate and_gate_0 (
        .inA(inA),
        .inB(inB),
        .out(out_and_gate)
    );

    // クロック生成： HIGH/LOWを繰り返す
    always begin
        clk = 1'b1;
        #(HALF_CYCLE) clk = 1'b0;
        #(HALF_CYCLE);  // #によって時間遅延を表現できる、#のあとに指定した単位時間だけ待って次の処理に移る
    end

    // テストシナリオ
    initial begin
        inA = 1'b0; inB = 1'b0;  // 初期化

        // 入力を変化させ、出力を表示
        inA = 1'b0; inB = 1'b0;
        #100 $display("inA=%b inB=%b out=%b", inA, inB, out_and_gate);
        inA = 1'b1; inB = 1'b0;
        #100 $display("inA=%b inB=%b out=%b", inA, inB, out_and_gate);  
        inA = 1'b0; inB = 1'b1;
        #100 $display("inA=%b inB=%b out=%b", inA, inB, out_and_gate);  
        inA = 1'b1; inB = 1'b1;
        #100 $display("inA=%b inB=%b out=%b", inA, inB, out_and_gate);
        // クロックの立ち上がり10回待って終了
        repeat(10) @(posedge clk); // 10回繰り返し
        $finish;
    end
endmodule
```
`reg`はフリップフロップ, 状態変数など, クロックなどに応じて値が変化するような用途に使います.  
Verilogの`.inA`表記はnamed port connectionと呼ばれる構文で, `.inA(inA)`の`.inA`はモジュール内で定義されたポート名で, `(inA)`はテストベンチ側の信号名です.  
`always`は, 継続的に実行されるプロセスを定義するブロックで, シミュレーション中に繰り返し実行されます.  
`clk = 1'b1`では, clk信号を論理1(HIGH)に設定します. ここで`1'b1`は1ビット(`1'`)のバイナリ(`b`)値「1(`1`)」を意味します.  
`#(HALF_CYCLE)`の`#`は時間遅延演算子です.ちなみに以下の二つは同じ意味です.
``` verilog
#(HALF_CYCLE) clk = 1'b0;
```
``` verilog
#(HALF_CYCLE);
clk = 1'b0;
```