
# interencdec
[Challenge](https://play.picoctf.org/practice/challenge/414)

## Description

Can you get the real meaning from this file.Download the file [here](https://artifacts.picoctf.net/c_titan/3/enc_flag).

## Hints

Engaging in various decoding processes is of utmost importance

## Solution

題目給的密文最後面兩個等號，推測為base64，解碼後也是兩個等號，再推測為base64：

```
# enc_flag:
YidkM0JxZGtwQlRYdHFhR3g2YUhsZmF6TnFlVGwzWVROclgya3lNRFJvYTJvMmZRPT0nCg==
# Base64decode:
d3BqdkpBTXtqaGx6aHlfazNqeTl3YTNrX2kyMDRoa2o2fQ
# Base64decode again:
wpjvJAM{jhlzhy_k3jy9wa3k_i204hkj6}
```

解碼後看到大括號，且開頭的wpjvJAM可以對應到picoCTF，透過小寫c跟大寫C對應到小寫j跟大寫J我們可以推測為ROT加密，把ROT的所有組合跑出來（[線上工具](https://theblob.org/rot.cgi)），找到pico開頭的即為答案：

```
ROT-0: wpjvJAM{jhlzhy_k3jy9wa3k_i204hkj6}
ROT-1: xqkwKBN{kimaiz_l3kz9xb3l_j204ilk6}
ROT-2: yrlxLCO{ljnbja_m3la9yc3m_k204jml6}
ROT-3: zsmyMDP{mkockb_n3mb9zd3n_l204knm6}
ROT-4: atnzNEQ{nlpdlc_o3nc9ae3o_m204lon6}
ROT-5: buoaOFR{omqemd_p3od9bf3p_n204mpo6}
ROT-6: cvpbPGS{pnrfne_q3pe9cg3q_o204nqp6}
ROT-7: dwqcQHT{qosgof_r3qf9dh3r_p204orq6}
ROT-8: exrdRIU{rpthpg_s3rg9ei3s_q204psr6}
ROT-9: fyseSJV{squiqh_t3sh9fj3t_r204qts6}
ROT-10: gztfTKW{trvjri_u3ti9gk3u_s204rut6}
ROT-11: haugULX{uswksj_v3uj9hl3v_t204svu6}
ROT-12: ibvhVMY{vtxltk_w3vk9im3w_u204twv6}
ROT-13: jcwiWNZ{wuymul_x3wl9jn3x_v204uxw6}
ROT-14: kdxjXOA{xvznvm_y3xm9ko3y_w204vyx6}
ROT-15: leykYPB{ywaown_z3yn9lp3z_x204wzy6}
ROT-16: mfzlZQC{zxbpxo_a3zo9mq3a_y204xaz6}
ROT-17: ngamARD{aycqyp_b3ap9nr3b_z204yba6}
ROT-18: ohbnBSE{bzdrzq_c3bq9os3c_a204zcb6}
ROT-19: picoCTF{caesar_d3cr9pt3d_b204adc6}
ROT-20: qjdpDUG{dbftbs_e3ds9qu3e_c204bed6}
ROT-21: rkeqEVH{ecguct_f3et9rv3f_d204cfe6}
ROT-22: slfrFWI{fdhvdu_g3fu9sw3g_e204dgf6}
ROT-23: tmgsGXJ{geiwev_h3gv9tx3h_f204ehg6}
ROT-24: unhtHYK{hfjxfw_i3hw9uy3i_g204fih6}
ROT-25: voiuIZL{igkygx_j3ix9vz3j_h204gji6}
```