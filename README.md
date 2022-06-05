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
