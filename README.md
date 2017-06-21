## Thrift2TS

Parse Thrift (IDL) to TypeScript.

[Thrift Doc](https://thrift.apache.org/docs/idl)

[TypeScript Doc](https://www.typescriptlang.org/docs/home.html)

## Install

npm
```
npm install thrift2ts -g
```

yarn
```
yarn add thrift2ts
```

## Usage

### CLI
```
thrift2ts -i [thrift file path] -o [typescript file output folder] -w [webApi import path]
```

sample
```
thrift2ts -i ./common.thrift -o ./services -w ./webApi
```

### normal package
```
var thrift2ts = require('thrift2ts');
var thriftCode = 'XXX';

var tsCode = thrift2ts(thriftCode, './webApi')
```

## Example

Thrift

```
namespace java com.company.javabusz
namespace go com.company.gobusz

include "./Common.thrift"

enum EmployeeType {
	Junior = 0,
	Senior = 1,
	Manager,
	Director = 0xa
}

struct Employee {
	1:required string name;
	2:optional i32 age;
	3:required map<string, i32> tasks;
}

exception NetworkException {
	1:required i32 code;
	2:required string message;
	3:optional string url;
}

const i32 year = 2017

const list<string> languages = ['Java', 'Go', 'JavaScript']

const map<string, i32> lanAges = {'Java': 20, 'Go': 8, 'JavaScript': 16}

const bool happy = true

// This is a map definition
typedef map<string, number> EmployeesCatlog // a tail comment

service EmployeeOperator {
	list<Employee> QueryEmployee(1:i32 age)
}

service EmployeeSalaryOperator extends EmployeeOperator {
	bool OperateEmployeeSalaryByType(1:EmployeeType type, 2:i64 amount, 2:string note);
}
```
Convert to TypeScript

```
/**
 * Auto generated by Thrift2Ts.
 *
 * Mon Jun 19 2017 22:42:06 GMT+0800 (CST)
 */

import webApi from "./webApi";

import * as Common from "./CommonService";

export const year: number = 2017; 

export const languages: string[] = ["Java", "Go", "JavaScript"]; 

export const lanAges: {[key: string]: number} = {"Java": 20, "Go": 8, "JavaScript": 16}; 

export const happy: boolean = true; 

export enum EmployeeType {
    Junior = 0,
    Senior = 1,
    Manager = 2,
    Director = 10
}

export interface NetworkException {
    code: number;
    message: string;
    url?: string;
}

export interface Employee {
    name: string;
    age?: number;
    tasks: {[key: string]: number};
}

export function QueryEmployee(age: number): Promise<Employee[]> {
    return webApi<Employee[]>("EmployeeOperator.QueryEmployee", { age })
}

export function OperateEmployeeSalaryByType(type: EmployeeType, amount: number, note: string): Promise<boolean> {
    return webApi<boolean>("EmployeeSalaryOperator.OperateEmployeeSalaryByType", { type, amount, note })
}


export default {
    QueryEmployee,
    OperateEmployeeSalaryByType
}

```

## Issues

### Why import webApi

Thrift service will exploded into functions which are used for RPC-liked call, a common API request instance is required, and accept method string, POST data as parameters. We donnot concern about which request approach(AJAX, Fetch) or libraries(axios, jQuery, fetch-io) you'd like to use, but you must provide the webApi implementation file path for importing.