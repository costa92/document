# Golang-RSA加密解密-数据无大小限制

## go 实现代码

```go
    package main

import (
    "bytes"
    "crypto"
    "crypto/rand"
    "crypto/rsa"
    "crypto/x509"
    "encoding/asn1"
    "encoding/base64"
    "encoding/pem"
    "errors"
    "fmt"
    "io"
    "io/ioutil"
)

const (
    CHAR_SET               = "UTF-8"
    BASE_64_FORMAT         = "UrlSafeNoPadding"
    RSA_ALGORITHM_KEY_TYPE = "PKCS8"
    RSA_ALGORITHM_SIGN     = crypto.SHA256
)

type XRsa struct {
    publicKey  *rsa.PublicKey
    privateKey *rsa.PrivateKey
}

// CreateKeys 生成密钥对
func CreateKeys(publicKeyWriter, privateKeyWriter io.Writer, keyLength int) error {
    // 生成私钥文件
    privateKey, err := rsa.GenerateKey(rand.Reader, keyLength)
    if err != nil {
        return err
    }
    derStream := MarshalPKCS8PrivateKey(privateKey)
    block := &pem.Block{
        Type:  "PRIVATE KEY",
        Bytes: derStream,
    }
    err = pem.Encode(privateKeyWriter, block)
    if err != nil {
        return err
    }
    // 生成公钥文件
    publicKey := &privateKey.PublicKey
    derPkix, err := x509.MarshalPKIXPublicKey(publicKey)
    if err != nil {
        return err
    }
    block = &pem.Block{
        Type:  "PUBLIC KEY",
        Bytes: derPkix,
    }
    err = pem.Encode(publicKeyWriter, block)
    if err != nil {
        return err
    }
    return nil
}
func NewXRsa(publicKey []byte, privateKey []byte) (*XRsa, error) {
    block, _ := pem.Decode(publicKey)
    if block == nil {
        return nil, errors.New("public key error")
    }
    pubInterface, err := x509.ParsePKIXPublicKey(block.Bytes)
    if err != nil {
        return nil, err
    }
    pub := pubInterface.(*rsa.PublicKey)
    block, _ = pem.Decode(privateKey)
    if block == nil {
        return nil, errors.New("private key error!")
    }
    priv, err := x509.ParsePKCS8PrivateKey(block.Bytes)
    if err != nil {
        return nil, err
    }
    pri, ok := priv.(*rsa.PrivateKey)
    if ok {
        return &XRsa{
            publicKey:  pub,
            privateKey: pri,
        }, nil
    } else {
        return nil, errors.New("private key not supported")
    }
}

// PublicEncrypt 公钥加密
func (r *XRsa) PublicEncrypt(data string) (string, error) {
    partLen := r.publicKey.N.BitLen()/8 - 11
    chunks := split([]byte(data), partLen)
    buffer := bytes.NewBufferString("")
    for _, chunk := range chunks {
        bytes, err := rsa.EncryptPKCS1v15(rand.Reader, r.publicKey, chunk)
        if err != nil {
            return "", err
        }
        buffer.Write(bytes)
    }
    return base64.RawURLEncoding.EncodeToString(buffer.Bytes()), nil
}

// PrivateDecrypt 私钥解密
func (r *XRsa) PrivateDecrypt(encrypted string) (string, error) {
    fmt.Println(encrypted)
    //raw,err := base64.StdEncoding.EncodeToString([]byte(encrypted))
    partLen := r.publicKey.N.BitLen() / 8
    raw ,err := base64.StdEncoding.DecodeString(encrypted)
    if err != nil{
       panic(err)
    }
    chunks := split(raw, partLen)
    buffer := bytes.NewBufferString("")
    for _, chunk := range chunks {
        decrypted, err := rsa.DecryptPKCS1v15(rand.Reader, r.privateKey, chunk)
        if err != nil {
            return "", err
        }
        buffer.Write(decrypted)
    }
    return buffer.String(), err
}

// Sign 数据加签
func (r *XRsa) Sign(data string) (string, error) {
    h := RSA_ALGORITHM_SIGN.New()
    h.Write([]byte(data))
    hashed := h.Sum(nil)
    sign, err := rsa.SignPKCS1v15(rand.Reader, r.privateKey, RSA_ALGORITHM_SIGN, hashed)
    if err != nil {
        return "", err
    }
    return base64.RawURLEncoding.EncodeToString(sign), err
}

// Verify 数据验签
func (r *XRsa) Verify(data string, sign string) error {
    h := RSA_ALGORITHM_SIGN.New()
    h.Write([]byte(data))
    hashed := h.Sum(nil)
    decodedSign, err := base64.RawURLEncoding.DecodeString(sign)
    if err != nil {
        return err
    }
    return rsa.VerifyPKCS1v15(r.publicKey, RSA_ALGORITHM_SIGN, hashed, decodedSign)
}
func MarshalPKCS8PrivateKey(key *rsa.PrivateKey) []byte {
    info := struct {
        Version             int
        PrivateKeyAlgorithm []asn1.ObjectIdentifier
        PrivateKey          []byte
    }{}
    info.Version = 0
    info.PrivateKeyAlgorithm = make([]asn1.ObjectIdentifier, 1)
    info.PrivateKeyAlgorithm[0] = asn1.ObjectIdentifier{1, 2, 840, 113549, 1, 1, 1}
    info.PrivateKey = x509.MarshalPKCS1PrivateKey(key)
    k, _ := asn1.Marshal(info)
    return k
}
func split(buf []byte, lim int) [][]byte {
    var chunk []byte
    chunks := make([][]byte, 0, len(buf)/lim+1)
    for len(buf) >= lim {
        chunk, buf = buf[:lim], buf[lim:]
        chunks = append(chunks, chunk)
    }
    if len(buf) > 0 {
        chunks = append(chunks, buf[:])
    }
    return chunks
}

func main() {

    // 1、读取公钥文件，获取公钥字节
    publicKeyBytes, err := ioutil.ReadFile("public.key")
    if err != nil {
       panic(err)
    }

    // 1、读取私钥文件，获取私钥字节
    privateKeyBytes, err := ioutil.ReadFile("private.key")
    if err != nil {
        panic(err)
    }

    fmt.Println(string(publicKeyBytes))
    xrsa, _ :=  NewXRsa(publicKeyBytes,privateKeyBytes)
    //data := "eBo8zlqOvyq4jUdBxt9Rm2Qcsi7Ko0WprKuhT0Y/rmnkYfwC1uCbFbnf7pbHJea1SKs1RY0On319wPyMG9a6GoLo49K21ZaBXl6HWt9lT6sdNefgMrJY9FCQgJTrgPywjBLENnKgjyACwcDWyPYfX6Abr8kZ1fMZ/laQc0VYKAkP/ewL4y7QNVUbxW7bhAS+DEb8ZYKe+S0H9Zrygsg9lK4VDS+ut74xJ9A1t9McdIeF7pa/30bnFbTNvgxxJPsEv0iZQkVp+1++b/9UXchKY2+Y3l9hpYmBgOQ5LVxlJaYWBe2YX5QDu8/siWDuLuD0iZIFCEAxV1JY1pgOoS6lEQ=="
      data := "eBo8zlqOvyq4jUdBxt9Rm2Qcsi7Ko0WprKuhT0Y/rmnkYfwC1uCbFbnf7pbHJea1SKs1RY0On319wPyMG9a6GoLo49K21ZaBXl6HWt9lT6sdNefgMrJY9FCQgJTrgPywjBLENnKgjyACwcDWyPYfX6Abr8kZ1fMZ/laQc0VYKAkP/ewL4y7QNVUbxW7bhAS+DEb8ZYKe+S0H9Zrygsg9lK4VDS+ut74xJ9A1t9McdIeF7pa/30bnFbTNvgxxJPsEv0iZQkVp+1++b/9UXchKY2+Y3l9hpYmBgOQ5LVxlJaYWBe2YX5QDu8/siWDuLuD0iZIFCEAxV1JY1pgOoS6lEQ=="
    result,err:= xrsa.PrivateDecrypt(data)

    fmt.Println(err)
    fmt.Println(result)
}

```


## php 实现代码

```php
<?php
if (! function_exists('url_safe_base64_encode')) {
    function url_safe_base64_encode ($data) {
        return str_replace(array('+','/', '='),array('-','_', ''), base64_encode($data));
    }
}

if (! function_exists('url_safe_base64_decode')) {
    function url_safe_base64_decode ($data) {
        $base_64 = str_replace(array('-','_'),array('+','/'), $data);
        return base64_decode($base_64);
    }
}

class XRsa
{
    const CHAR_SET = "UTF-8";
    const BASE_64_FORMAT = "UrlSafeNoPadding";
    const RSA_ALGORITHM_KEY_TYPE = OPENSSL_KEYTYPE_RSA;
    const RSA_ALGORITHM_SIGN = OPENSSL_ALGO_SHA256;

    protected $public_key;
    protected $private_key;
    protected $key_len;

    public function __construct($pub_key, $pri_key = null)
    {
        $this->public_key = $this->readRsa($pub_key);
        $this->private_key = $this->readRsa($pri_key);

        $pub_id = openssl_get_publickey($this->public_key);
        $this->key_len = openssl_pkey_get_details($pub_id)['bits'];
    }

    /*
     * 创建密钥对
     */
    public static function createKeys($key_size = 2048)
    {
        $config = array(
            "private_key_bits" => $key_size,
            "private_key_type" => self::RSA_ALGORITHM_KEY_TYPE,
        );
        $res = openssl_pkey_new($config);
        openssl_pkey_export($res, $private_key);
        $public_key_detail = openssl_pkey_get_details($res);
        $public_key = $public_key_detail["key"];

        return [
            "public_key" => $public_key,
            "private_key" => $private_key,
        ];
    }

    /*
     * 公钥加密
     */
    public function publicEncrypt($data)
    {
        $encrypted = '';
        $part_len = $this->key_len / 8 - 11;
        $parts = str_split($data, $part_len);

        foreach ($parts as $part) {
            $encrypted_temp = '';
            openssl_public_encrypt($part, $encrypted_temp, $this->public_key);
            $encrypted .= $encrypted_temp;
        }

        return url_safe_base64_encode($encrypted);
    }

    /*
     * 私钥解密
     */
    public function privateDecrypt($encrypted)
    {
        $decrypted = "";
        $part_len = $this->key_len / 8;
        $base64_decoded = url_safe_base64_decode($encrypted);
        $parts = str_split($base64_decoded, $part_len);

        foreach ($parts as $part) {
            $decrypted_temp = '';
            openssl_private_decrypt($part, $decrypted_temp,$this->private_key);
            $decrypted .= $decrypted_temp;
        }
        return $decrypted;
    }

    /*
     * 私钥加密
     */
    public function privateEncrypt($data)
    {
        $encrypted = '';
        $part_len = $this->key_len / 8 - 11;
        $parts = str_split($data, $part_len);

        foreach ($parts as $part) {
            $encrypted_temp = '';
            openssl_private_encrypt($part, $encrypted_temp, $this->private_key);
            $encrypted .= $encrypted_temp;
        }

        return url_safe_base64_encode($encrypted);
    }

    /*
     * 公钥解密
     */
    public function publicDecrypt($encrypted)
    {
        $decrypted = "";
        $part_len = $this->key_len / 8;
        $base64_decoded = url_safe_base64_decode($encrypted);
        $parts = str_split($base64_decoded, $part_len);

        foreach ($parts as $part) {
            $decrypted_temp = '';
            openssl_public_decrypt($part, $decrypted_temp,$this->public_key);
            $decrypted .= $decrypted_temp;
        }
        return $decrypted;
    }

    /*
     * 数据加签
     */
    public function sign($data)
    {
        openssl_sign($data, $sign, $this->private_key, self::RSA_ALGORITHM_SIGN);

        return url_safe_base64_encode($sign);
    }

    /*
     * 数据签名验证
     */
    public function verify($data, $sign)
    {
        $pub_id = openssl_get_publickey($this->public_key);
        $res = openssl_verify($data, url_safe_base64_decode($sign), $pub_id, self::RSA_ALGORITHM_SIGN);

        return $res;
    }

     /**
     * 读取证书
     * @param $file
     * @return bool|string
     * @throws Exception
     */
    public function readRsa($file)
    {
        if (!file_exists($file)) {
            throw new Exception($file . '文件不存在');
        }
        $fp = fopen($file, "r"); // 打开秘钥文件
        $key = fread($fp, 8192); // 读取秘钥文件
        fclose($fp);  // 关闭秘钥文件
        return $key;
    }


}


$data = "eBo8zlqOvyq4jUdBxt9Rm2Qcsi7Ko0WprKuhT0Y/rmnkYfwC1uCbFbnf7pbHJea1SKs1RY0On319wPyMG9a6GoLo49K21ZaBXl6HWt9lT6sdNefgMrJY9FCQgJTrgPywjBLENnKgjyACwcDWyPYfX6Abr8kZ1fMZ/laQc0VYKAkP/ewL4y7QNVUbxW7bhAS+DEb8ZYKe+S0H9Zrygsg9lK4VDS+ut74xJ9A1t9McdIeF7pa/30bnFbTNvgxxJPsEv0iZQkVp+1++b/9UXchKY2+Y3l9hpYmBgOQ5LVxlJaYWBe2YX5QDu8/siWDuLuD0iZIFCEAxV1JY1pgOoS6lEQ==";
$XRsa = new XRsa("public.key","private.key");
$result = $XRsa->privateDecrypt($data);
var_dump($result);

```