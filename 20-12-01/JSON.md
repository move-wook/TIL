# JSON

-javaScript Object Notation (데이터 포멧) {key:value}

-데이터를 주고받을때 사용할수 있는 간단한 파일 포셈 -텍스트 기반 가볍다 -읽기도 변해서 사람과 기계모두 읽고 쓰기 편한다 -키와 벨류로 이루어져있다. -직렬화를 하고 데이터 전송할때 사용한다 -프로그램 언어나 플랫폼에 독립적이다. 서로 다른 시스템간에 객체를 교환하기도 쉽다.\*\*

## Object to JSON

    //stringfy(obj)
    let json=JSON.stringify(true);
    console.log(json)->//true

    json=JSON.stringify(['apple','banana']);
    console.log(json)->//["apple","banana"] json의 규격 사항

    const rabbit={
        name:'tori',
        color:'white',
        size:null,
        birthData:new Data(),//Object
        jump:()=>{
            console.log(`${name}can jump!`);
        },
    }
    json=JSON.stringify(rabbit);
    console.log(json)->// {"name":"tori","color":"white","size":json.null,"birthData":"2020-12-01T21:20:22.670z"}
    함수는 json에 포함 x
    symbol 도 포함 x

## JSON to Object

    const obj =JSON.parse(변환하고 싶은 json)
