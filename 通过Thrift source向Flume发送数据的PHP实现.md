demo1.php
========================================
#!/usr/bin/env php
<?php

namespace flume\php;

error_reporting(E_ALL);

require_once __DIR__.'/../../lib/php/lib/Thrift/ClassLoader/ThriftClassLoader.php';

use Thrift\ClassLoader\ThriftClassLoader;

$GEN_DIR = realpath(dirname(__FILE__).'/..').'/gen-php';

$loader = new ThriftClassLoader();
$loader->registerNamespace('Thrift', __DIR__ . '/../../lib/php/lib');
$loader->registerDefinition('flume', $GEN_DIR);
$loader->register();

/*
 * Licensed to the Apache Software Foundation (ASF) under one
 * or more contributor license agreements. See the NOTICE file
 * distributed with this work for additional information
 * regarding copyright ownership. The ASF licenses this file
 * to you under the Apache License, Version 2.0 (the
 * "License"); you may not use this file except in compliance
 * with the License. You may obtain a copy of the License at
 *
 *   http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing,
 * software distributed under the License is distributed on an
 * "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
 * KIND, either express or implied. See the License for the
 * specific language governing permissions and limitations
 * under the License.
 */

use Thrift\Protocol\TCompactProtocol;
use Thrift\Transport\TSocket;
use Thrift\Transport\THttpClient;
use Thrift\Transport\TFramedTransport;
use Thrift\Exception\TException;

try {
  $client = new FlumeClient('localhost', 4141);
  $a = ['a' => 'hello', '' => 'world'];
  $flumeEvent = new \flume\ThriftFlumeEvent(['headers' => $a, 'body' => 'events under hello world100']);
  var_dump($flumeEvent);

  $client->send($flumeEvent);
  echo 1212;

  //批发
  $flumeEvents = [];
  for($i = 10; $i <= 20; $i++){
    $body = 'events under hello world' . $i;
    $flumeEvents[] = new \flume\ThriftFlumeEvent(['headers' => $a, 'body' => $body]);
  }
  $client->send_batch($flumeEvents);
  var_dump($flumeEvents);

  $client->close();

} catch (TException $tx) {
  print 'TException4: '.$tx->getMessage()."\n";
}

class _Transport{
  protected $thrift_host = '';
  protected $thrift_port = '';

  protected $_socket = '';
  protected $_transport = '';

  public function __construct($thrift_host, $thrift_port) {
    $this->thrift_host = $thrift_host;
    $this->thrift_port = $thrift_port;

    $this->_socket = new TSocket($thrift_host, $thrift_port);
    $this->_socket->setSendTimeout(10000);
    $this->_socket->setRecvTimeout(20000);

    $this->_transport = new TFramedTransport($this->_socket);
  }

  public function connect(){
    try {
      if(!$this->is_open()){
        $this->_transport = new TFramedTransport($this->_socket);
        $this->_transport->open();
      }
    } catch (TException $e) {
      print 'TException3: '.$e->getMessage()."\n";
      $this->close();
    }
  }

  public function is_open(){
    return $this->_transport->isOpen();
  }

  public function get_transport(){
    return $this->_transport;
  }

  public function close(){
    $this->_transport->close();
  }
}

class FlumeClient{
  protected $_transObj = null;
  protected $_protocol = null;
  protected $client = null;

  public function __construct($thrift_host, $thrift_port) {
    $this->_transObj = new _Transport($thrift_host, $thrift_port);
    $this->_protocol = new TCompactProtocol($this->_transObj->get_transport());
    $this->client = new \flume\ThriftSourceProtocolClient($this->_protocol, $this->_protocol);
    $this->_transObj->connect();
  }

  public function send($event){
    try {
      $this->client->append($event);
    } catch (TException $e) {
      print 'TException1: '.$e->getMessage()."\n";
    } finally{
      $this->_transObj->connect();
    }
  }

  public function send_batch($events){
    try {
      $this->client->appendBatch($events);
    } catch (TException $e) {
      print 'TException2: '.$e->getMessage()."\n";
    } finally{
      $this->_transObj->connect();
    }
  }

  public function close(){
    $this->_transObj->close();
  }

}

?>


demo2.php
========================================
#!/usr/bin/env php
<?php

namespace flume\php;

error_reporting(E_ALL);

require_once __DIR__.'/../../lib/php/lib/Thrift/ClassLoader/ThriftClassLoader.php';

use Thrift\ClassLoader\ThriftClassLoader;

$GEN_DIR = realpath(dirname(__FILE__).'/..').'/gen-php';

$loader = new ThriftClassLoader();
$loader->registerNamespace('Thrift', __DIR__ . '/../../lib/php/lib');
$loader->registerDefinition('flume', $GEN_DIR);
$loader->register();

use Thrift\Protocol\TCompactProtocol;
use Thrift\Transport\TSocket;
use Thrift\Transport\THttpClient;
use Thrift\Transport\TFramedTransport;
use Thrift\Exception\TException;

try {
  $socket = new TSocket('localhost', 4141);
  $socket->setSendTimeout(10000);
  $socket->setRecvTimeout(20000);

  $transport = new TFramedTransport($socket);
  $protocol = new TCompactProtocol($transport);
  $client = new \flume\ThriftSourceProtocolClient($protocol, $protocol);

  $transport->open();
  $a = ['a' => 'hello', '' => 'world'];
  $flumeEvent = new \flume\ThriftFlumeEvent();
  $flumeEvent->headers = $a;
  $flumeEvent->body = 'events under hello world2';
  var_dump($flumeEvent);

  //单发
  $client->append($flumeEvent);

  //批发
  $flumeEvents = [];
  for($i = 0; $i <= 10; $i++){
    $body = 'events under hello world' . $i;
    $flumeEvents[] = new \flume\ThriftFlumeEvent(['headers' => $a, 'body' => $body]);
  }
  $client->appendBatch($flumeEvents);
  var_dump($flumeEvents);

  $transport->close();

} catch (TException $tx) {
  print 'TException: '.$tx->getMessage()."\n";
}