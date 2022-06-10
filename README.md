```
<?php
    class Api{
        public const COMPILE_ENDPOINT = 'https://api.hackerearth.com/v4/partner/code-evaluation/submissions/';
        public const CLIENT_SECRET_KEY = '807b8090d758b4e55be48834bfe2b93e9a71efae';
        public function compile($data){
            $ch = curl_init();
            curl_setopt($ch, CURLOPT_URL, Api::COMPILE_ENDPOINT);
            curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
            curl_setopt($ch, CURLOPT_POST, 1);
            curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($data));

            $headers = array();
            $headers[] = 'Cache-Control: no-cache';
            $headers[] = 'Client-Secret: 807b8090d758b4e55be48834bfe2b93e9a71efae';
            $headers[] = 'Content-Type: application/json';
            curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);

            $result = curl_exec($ch);
            return json_decode($result)->status_update_url;
        }
    

        public function run($url){
            $ch = curl_init();
            echo $url;
            curl_setopt($ch, CURLOPT_URL, $url);
            curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
            curl_setopt($ch, CURLOPT_CUSTOMREQUEST, 'GET');
            curl_setopt($ch, CURLOPT_TIMEOUT, 5);

            $headers = array();
            $headers[] = 'Client-Secret: 807b8090d758b4e55be48834bfe2b93e9a71efae';
            curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);

            $r = true;
            $result = null;
            $seconds = 0;
            do{
                $result = curl_exec($ch);
                if (curl_errno($ch)) {
                    echo 'Error:' . curl_error($ch);
                }
                // echo var_dump(json_decode($result)->request_status->code); die();
                $code = json_decode($result)->request_status->code ?? "";
                if ($code  =="REQUEST_COMPLETED" || $code == "REQUEST_FAILED" || $seconds == 5 ) {
                    $r = false;
                    break;
                }
                $seconds++;
                sleep(1);
            } while($r);
            curl_close($ch);
            return json_decode($result);
        }
    }

    // $api = new Api();
    // $url = $api->compile([
    //     'lang' => "PYTHON",
    //     'source' => "print 'Hello World'"
    // ]);
    // // $url ="https://api.hackerearth.com/v4/partner/code-evaluation/submissions/0002c583-4930-4b1c-8448-e13f4255ede9/";
    // $res = $api->run($url);
    // if ($res->request_status->code == "REQUEST_FAILED" ||$res->request_status->code == "CODE_COMPILED" ){
    //     echo $res->result->compile_status;
    // }
    // else{
    //     $res_result = $res->result->run_status;
    //     $compile_status = $res->result->compile_status;
    //     echo var_dump($res->result);
    // }

    
    $servername = "localhost";
    $username = "root";
    $password = "";

    // Create connection
    $conn = mysqli_connect($servername, $username, $password, "hackerrank");
    $sql = "select * from submissions where id = 1";
    $result = $conn->query($sql);
    $source = "";
    $lang = "";
    if ($result->num_rows > 0) {
        while($row = $result->fetch_assoc()) {
            $source = $row["source"];
            $lang = $row["lang"];
        }
    }
  
    $sql = "select * from testcases where challenge_id = 1";
    $result = mysqli_query($conn, $sql);
    $api = new Api();
    if ($result->num_rows > 0) {
        while($row = $result->fetch_assoc()) {
            $url = $api->compile([
                'lang' => $lang,
                'source' => $source,
                'input' =>  $row["input"]
            ]);
            $res = $api->run($url);
            if ($res->request_status->code == "REQUEST_FAILED" ||$res->request_status->code == "CODE_COMPILED" ){
                echo $res->result->compile_status;
            }
            else{
                $res_result = $res->result->run_status;
                $compile_status = $res->result->compile_status;
                $output= $res_result->output;
                $id = $row["id"];
                $sql = "INSERT INTO results(submission_id, url, testcase_id) VALUES ('1', '$output', '$id')";
                mysqli_query($conn, $sql);
            }
        }
    } else {
        echo "0 results";
    }
?>
```

```
<?php
    class Judgo0API {
        public static function createBatchSubmissions($start){
            $curl = curl_init();
            $data = [
                "submissions" => [
                    [
                        "source_code" => "I2luY2x1ZGUgPHN0ZGlvLmg+DQppbnQgbWFpbigpIHsgICAgDQogDQogICAgaW50IG51bTEsIG51bTIsIHN1bTsNCiAgICBzY2FuZigiJWQgJWQiLCAmbnVtMSwgJm51bTIpOw0KIA0KICAgIC8vIHRpbmggdG9uZyAyIHNvDQogICAgc3VtID0gbnVtMSArIG51bTI7ICAgICAgDQogICAgIA0KICAgIHByaW50ZigiJWQiLCBzdW0pOw0KICAgIHJldHVybiAwOw0KfQ==",
                        "language_id" => 75,
                        "stdin" =>"MjUgMzA=",
                        "expected_output" => "NTU="
                    ],
                    [
                        "source_code" => "I2luY2x1ZGUgPHN0ZGlvLmg+DQppbnQgbWFpbigpIHsgICAgDQogDQogICAgaW50IG51bTEsIG51bTIsIHN1bTsNCiAgICBzY2FuZigiJWQgJWQiLCAmbnVtMSwgJm51bTIpOw0KIA0KICAgIC8vIHRpbmggdG9uZyAyIHNvDQogICAgc3VtID0gbnVtMSArIG51bTI7ICAgICAgDQogICAgIA0KICAgIHByaW50ZigiJWQiLCBzdW0pOw0KICAgIHJldHVybiAwOw0KfQ==",
                        "language_id" => 75,
                        "stdin" =>"MjUgMzA=",
                        "expected_output" => "NTU="
                    ],
                    [
                        "source_code" => "I2luY2x1ZGUgPHN0ZGlvLmg+DQppbnQgbWFpbigpIHsgICAgDQogDQogICAgaW50IG51bTEsIG51bTIsIHN1bTsNCiAgICBzY2FuZigiJWQgJWQiLCAmbnVtMSwgJm51bTIpOw0KIA0KICAgIC8vIHRpbmggdG9uZyAyIHNvDQogICAgc3VtID0gbnVtMSArIG51bTI7ICAgICAgDQogICAgIA0KICAgIHByaW50ZigiJWQiLCBzdW0pOw0KICAgIHJldHVybiAwOw0KfQ==",
                        "language_id" => 75,
                        "stdin" =>"MTIzNDU2NzggODc2NTQzMjE=",
                        "expected_output" => "OTk5OTk5OTk="
                    ],
                    [
                        "source_code" => "I2luY2x1ZGUgPHN0ZGlvLmg+DQppbnQgbWFpbigpIHsgICAgDQogDQogICAgaW50IG51bTEsIG51bTIsIHN1bTsNCiAgICBzY2FuZigiJWQgJWQiLCAmbnVtMSwgJm51bTIpOw0KIA0KICAgIC8vIHRpbmggdG9uZyAyIHNvDQogICAgc3VtID0gbnVtMSArIG51bTI7ICAgICAgDQogICAgIA0KICAgIHByaW50ZigiJWQiLCBzdW0pOw0KICAgIHJldHVybiAwOw0KfQ==",
                        "language_id" => 75,
                        "stdin" =>"MTIzNDU2NyA4NzY1NDMy",
                        "expected_output" => "OTk5OTk5OQ=="
                    ],
                    [
                        "source_code" => "I2luY2x1ZGUgPHN0ZGlvLmg+DQppbnQgbWFpbigpIHsgICAgDQogDQogICAgaW50IG51bTEsIG51bTIsIHN1bTsNCiAgICBzY2FuZigiJWQgJWQiLCAmbnVtMSwgJm51bTIpOw0KIA0KICAgIC8vIHRpbmggdG9uZyAyIHNvDQogICAgc3VtID0gbnVtMSArIG51bTI7ICAgICAgDQogICAgIA0KICAgIHByaW50ZigiJWQiLCBzdW0pOw0KICAgIHJldHVybiAwOw0KfQ==",
                        "language_id" => 75,
                        "stdin" =>"MTQ3IDc0MQ==",
                        "expected_output" => "ODg4OA=="
                    ],
                    [
                        "source_code" => "I2luY2x1ZGUgPHN0ZGlvLmg+DQppbnQgbWFpbigpIHsgICAgDQogDQogICAgaW50IG51bTEsIG51bTIsIHN1bTsNCiAgICBzY2FuZigiJWQgJWQiLCAmbnVtMSwgJm51bTIpOw0KIA0KICAgIC8vIHRpbmggdG9uZyAyIHNvDQogICAgc3VtID0gbnVtMSArIG51bTI7ICAgICAgDQogICAgIA0KICAgIHByaW50ZigiJWQiLCBzdW0pOw0KICAgIHJldHVybiAwOw0KfQ==",
                        "language_id" => 75,
                        "stdin" =>"MTQ3IDc0MQ==",
                        "expected_output" => "ODg4OA=="
                    ],
                    [
                        "source_code" => "I2luY2x1ZGUgPHN0ZGlvLmg+DQppbnQgbWFpbigpIHsgICAgDQogDQogICAgaW50IG51bTEsIG51bTIsIHN1bTsNCiAgICBzY2FuZigiJWQgJWQiLCAmbnVtMSwgJm51bTIpOw0KIA0KICAgIC8vIHRpbmggdG9uZyAyIHNvDQogICAgc3VtID0gbnVtMSArIG51bTI7ICAgICAgDQogICAgIA0KICAgIHByaW50ZigiJWQiLCBzdW0pOw0KICAgIHJldHVybiAwOw0KfQ==",
                        "language_id" => 75,
                        "stdin" =>"MTQ3IDc0MQ==",
                        "expected_output" => "ODg4OA=="
                    ],
                    [
                        "source_code" => "I2luY2x1ZGUgPHN0ZGlvLmg+DQppbnQgbWFpbigpIHsgICAgDQogDQogICAgaW50IG51bTEsIG51bTIsIHN1bTsNCiAgICBzY2FuZigiJWQgJWQiLCAmbnVtMSwgJm51bTIpOw0KIA0KICAgIC8vIHRpbmggdG9uZyAyIHNvDQogICAgc3VtID0gbnVtMSArIG51bTI7ICAgICAgDQogICAgIA0KICAgIHByaW50ZigiJWQiLCBzdW0pOw0KICAgIHJldHVybiAwOw0KfQ==",
                        "language_id" => 75,
                        "stdin" =>"MTQ3IDc0MQ==",
                        "expected_output" => "ODg4OA=="
                    ],
                    [
                        "source_code" => "I2luY2x1ZGUgPHN0ZGlvLmg+DQppbnQgbWFpbigpIHsgICAgDQogDQogICAgaW50IG51bTEsIG51bTIsIHN1bTsNCiAgICBzY2FuZigiJWQgJWQiLCAmbnVtMSwgJm51bTIpOw0KIA0KICAgIC8vIHRpbmggdG9uZyAyIHNvDQogICAgc3VtID0gbnVtMSArIG51bTI7ICAgICAgDQogICAgIA0KICAgIHByaW50ZigiJWQiLCBzdW0pOw0KICAgIHJldHVybiAwOw0KfQ==",
                        "language_id" => 75,
                        "stdin" =>"MTQ3IDc0MQ==",
                        "expected_output" => "ODg4OA=="
                    ],
                    [
                        "source_code" => "I2luY2x1ZGUgPHN0ZGlvLmg+DQppbnQgbWFpbigpIHsgICAgDQogDQogICAgaW50IG51bTEsIG51bTIsIHN1bTsNCiAgICBzY2FuZigiJWQgJWQiLCAmbnVtMSwgJm51bTIpOw0KIA0KICAgIC8vIHRpbmggdG9uZyAyIHNvDQogICAgc3VtID0gbnVtMSArIG51bTI7ICAgICAgDQogICAgIA0KICAgIHByaW50ZigiJWQiLCBzdW0pOw0KICAgIHJldHVybiAwOw0KfQ==",
                        "language_id" => 75,
                        "stdin" =>"MTQ3IDc0MQ==",
                        "expected_output" => "ODg4OA=="
                    ],
                    [
                        "source_code" => "I2luY2x1ZGUgPHN0ZGlvLmg+DQppbnQgbWFpbigpIHsgICAgDQogDQogICAgaW50IG51bTEsIG51bTIsIHN1bTsNCiAgICBzY2FuZigiJWQgJWQiLCAmbnVtMSwgJm51bTIpOw0KIA0KICAgIC8vIHRpbmggdG9uZyAyIHNvDQogICAgc3VtID0gbnVtMSArIG51bTI7ICAgICAgDQogICAgIA0KICAgIHByaW50ZigiJWQiLCBzdW0pOw0KICAgIHJldHVybiAwOw0KfQ==",
                        "language_id" => 75,
                        "stdin" =>"MTQ3IDc0MQ==",
                        "expected_output" => "ODg4OA=="
                    ]
                ]
            ];
            curl_setopt_array($curl, [
                CURLOPT_URL => "https://judge0-ce.p.rapidapi.com/submissions/batch?base64_encoded=true",
                CURLOPT_RETURNTRANSFER => true,
                CURLOPT_FOLLOWLOCATION => true,
                CURLOPT_ENCODING => "",
                CURLOPT_MAXREDIRS => 10,
                CURLOPT_TIMEOUT => 30,
                CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
                CURLOPT_CUSTOMREQUEST => "POST",
                CURLOPT_POSTFIELDS => json_encode($data),
                CURLOPT_HTTPHEADER => [
                    "Content-Type: application/json",
                    "X-RapidAPI-Host: judge0-ce.p.rapidapi.com",
                    "X-RapidAPI-Key: 098e3d9739msh4b746e012d2caa5p1246bajsn7e357dcbc876",
                    "content-type: application/json"
                ],
            ]);

            $response = curl_exec($curl);
            $err = curl_error($curl);

            curl_close($curl);

            if ($err) {
                echo "cURL Error #:" . $err;
            } else {
                $results = json_decode($response);
                $str = "";
                foreach ($results as $key => $result) {
                    $str .= $result->token .",";
                }
                $str = trim($str, ',');
                echo $str;
                $param = ["tokens" => $str];
                return static::getBatchSubmissions($param, $start);
            }
        }

        public static function getBatchSubmissions($param, $start){
            $dung = false;
            $res = "";
            $curl = curl_init();

            curl_setopt_array($curl, [
                CURLOPT_URL => "https://judge0-ce.p.rapidapi.com/submissions/batch?".http_build_query($param)."&base64_encoded=true&fields=status,language,time,expected_output",
                CURLOPT_RETURNTRANSFER => true,
                CURLOPT_FOLLOWLOCATION => true,
                CURLOPT_ENCODING => "",
                CURLOPT_MAXREDIRS => 10,
                CURLOPT_TIMEOUT => 30,
                CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
                CURLOPT_CUSTOMREQUEST => "GET",
                CURLOPT_HTTPHEADER => [
                    "X-RapidAPI-Host: judge0-ce.p.rapidapi.com",
                    "X-RapidAPI-Key: 098e3d9739msh4b746e012d2caa5p1246bajsn7e357dcbc876"
                ],
            ]);

            do{
    
                $response = curl_exec($curl);
                $res = json_decode($response)->submissions;
                $arr_count = array_reduce($res, function($su, $item){
                    if ($item->status->id >= 3)  return ++$su;
                    return $su;
                }, 0);

                if (count($res) == $arr_count){
                    $dung = true;
                }
                if (!$dung) sleep(1);
            }
            while (!$dung);
            curl_close($curl);
            echo "end1: ". (microtime(true) - $start);
            return $res;
        }
    }
    $start =microtime(true);
    $res =Judgo0API::createBatchSubmissions($start);
    echo "end2: ". (microtime(true) - $start);
    echo "<pre>";
    echo var_dump($res);
    echo "</pre>";
```
