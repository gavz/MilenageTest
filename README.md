# MilenageTest
## A 3G/4G/5G authentication test troubleshooting tool written in c++
  This small tool could be used to verify whether the MAC value or RES value exchanged during 3G/4G/5G authentication procude is correct or not.
  During my work in 3G/4G/5G authentication test and troubleshooting, there have been many cases that UE failed to pass the authentication procedure with core network. Usually it caused by mismatched secret key or OP settings, which results in "MAC failure" error or "RES value mismatched". In that case, we need a tool to verify that the MAC sent by core network or RES value sent by UE is correct, by computing those values based on secret key/OP/RAND value.
  So I came up with this idea to write a small tool in C, to manually compute the MAC/RES/RES* value and compare it to the one we received in authentication request and response message in pcap file, see whether it's matched. By that, we can tell which side is giving wrong computation result, and whether it's a potential problem of mismatched secret key/OP.
# supported algorithm
  Milenage algorithm defined in 3GPP TS 35.206 for 3G/4G/5G MAC value calculation and 3G/4G RES value calculation.
  
  HMAC_SHA256 defined in 3GPP TS 33.501 for 5G RES* value calculation.
# dependencies
  aperezdc/hmac-sha256: https://github.com/aperezdc/hmac-sha256 , HMAC-SHA256 implemented in C.
  
  The source code of milenage algorithm is from 3GPP TS 35.206
  
  The source code of HMAC-SHA256 is from https://github.com/aperezdc/hmac-sha256
# how to compile:
   the milenage.cpp file uses raw string literal introduced in c++11 to print out source code on the fly, so please compile it using g++, for example:
   
    g++  -std=c++11 hmac-sha256.c sha256.c milenage.cpp -o output_file_name
# how to use this tool:
  Simply compile the c code and run it, select the menu in below screenshot and let it compute the MAC and RES value (or compute RES* in 5G case) based on the secret key, OP, RAND, AUTN value you manually input. You need to have secret key of UE, OP value of network beforehand. Also need to get the RAND/AUTN value from authentication request message sent by AMF/MME/SGSN,you can do it usually by wireshark capture.
  
  below is the example:
  ![input](/images/input.png.jpg)
  compare it to the corresponding value in wireshark to verify it:
  ![wireshark-screen](/images/wireshark-screenshot.png.jpg)
# the basic idea of how to compute the MAC/RES/RES* based on 3GPP spec:
  The main algorithm used by 3G/4G/5G authentication procedure is Milenage which is defined in 3GPP TS 35.205.
  It defines the f1/f2/f3/f4/f5 functions used to compute MAC/RES/CK/IK/AK , with sample source code attached in specification.
  So this tool uses the source from that spec to compute the MAC/RES based on the secret key(K) and OP,RAND input by user.
  Keys generated by milenage algorithm:
  ![milenage1](/images/milenage1.png)
  Defintion of f1/f2/f3/f4/f5 functions:
  ![milenage2](/images/milenage2.png)
  
  To compute 5G RES*, there is one additional algorithm HMAC_SHA256 needed, and the derivation function is defined in Annex A.4 of 3GPP TS 33.501 as below, the input is SNN,RAND,RES,CK||IK,and the KDF is HMAC_256.
  ![res](/images/RES_STAR.png)
  
