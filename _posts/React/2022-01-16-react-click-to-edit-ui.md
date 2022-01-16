---
layout: post
title: React Click-to-Edit UI
subtitle: React Reference
categories: React
tags: [React, Click-to-Edit-UI]
---

Click-to-Edit UI
------------

Click-to-Edit UI란 해당 페이지에서 수정하고 싶은 컨텐츠를 클릭 혹은 더블클릭을 해서 손쉽게 수정 할 수 있는 User Interface를 말한다.

본론으로 바로 넘어가서 코드를 확인해보자!

#### React에서 Click-to-Edit UI 구현하기 ####

```jsx
import React, {useState} from 'react';

const [isEditable, setIsEditable] = useState(false);
const [content, setContent] = useState('default content');

const Content = () => {
  return (
	<div>
	  {isEditable ?
	  <textarea defaultValue={content} onKeyDown={(e) => {
		if(e.key === 'Enter') {
		  setIsEditable(false);
		  setContent(e.target.value);
		}
	  }}/> :
		<p onDoubleClick={() => setIsEditable(true)}>{content}</p>}
	</div>
  );
}

export default Content;
```

위의 코드와 같이 p 요소를 더블 클릭하면 해당 컨텐츠를 수정할 수 있도록 textare 요소가 나오고 수정을 해준후

Enter 키를 눌러 p 요소의 컨텐츠를 바꿀 수 있다.

### 마치며 ###

처음 해보는 것이 었는데 개념과 구현자체가 어려운 것은 아니였고 더 나은 유저 경험을 제공해줄 수 있기 때문에

웹 어플리케이션을 만들때 이러한 부분을 신경써서 만들면 좋을것 같다고 생각했다.
