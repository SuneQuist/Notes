# Script for Web Console, to delete LinkedIn contacts in mass.

Just click right-click on the LinkedIn contacts page and then choose inspect > console. (Or just press F12 / Ctrl + Shift + J)
Then paste in this script.
The loop is set to 4, since the first 4 in the list I did not wish to delete.

<br/><br/>
```js
function getDeletionTags() {
    const people = document.querySelectorAll('[data-view-name="connections-list"]');
    const people_hrefs = Array.from(people).map(person => person.querySelectorAll("a")[2].href);
    const deletion_tags = people_hrefs.map(href => href.substring(0, href.length - 1).split('/').pop());
    return deletion_tags;
}

function getCsrfFromCookie() {
    const match = document.cookie.match(/JSESSIONID="?(.*?)"?;/);
    if (match) {
        return decodeURIComponent(match[1]);
    }
    return "";
}

function linkedinDeletePerson(persons_tag) {
    const deletionString = {"requestId":"com.linkedin.sdui.mynetwork.RemoveConnectionId","serverRequest":{"$type":"proto.sdui.actions.core.ServerRequest","requestId":"com.linkedin.sdui.mynetwork.RemoveConnectionId","requestedArguments":{"$type":"proto.sdui.actions.requests.RequestedArguments","payload":{"disconnectVanityName": persons_tag},"requestedStateKeys":[],"requestMetadata":{"$type":"proto.sdui.common.RequestMetadata"}},"isStreaming":false,"rumPageKey":""},"states":[],"requestedArguments":{"$type":"proto.sdui.actions.requests.RequestedArguments","payload":{"disconnectVanityName": persons_tag},"requestedStateKeys":[],"requestMetadata":{"$type":"proto.sdui.common.RequestMetadata"},"states":[]}}

    const headers = new Headers();
    headers.append("csrf-token", getCsrfFromCookie());
    headers.append("Content-Type", "application/json");

    fetch("https://www.linkedin.com/flagship-web/rsc-action/actions/server-request?sduiid=com.linkedin.sdui.mynetwork.RemoveConnectionId", {
        method: "POST",
        body: JSON.stringify(deletionString),
        headers: headers,
        credentials: "include"
    });
}

let list = getDeletionTags();
for (let i = 4; i < list.length; i ++) {
    linkedinDeletePerson(list[i]);
}
```
