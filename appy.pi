import requests
from flask import Flask, request, Response

app = Flask(__name__)
TARGET = "https://dale-allen.oliver.com"  # backend server

@app.before_request
def proxy():
    # Build the proxied URL (with query string)
    url = f"{TARGET}{request.full_path}"
    if url.endswith("?"):  # remove trailing '?' if no query params
        url = url[:-1]

    # Forward headers (but remove ones that can break requests)
    headers = dict(request.headers)
    headers.pop("Host", None)
    headers.pop("Content-Length", None)

    # Forward the request
    resp = requests.request(
        method=request.method,
        url=url,
        headers=headers,
        data=request.get_data(),
        cookies=request.cookies,
        allow_redirects=False,
        stream=True,
    )

    # Build Flask response with streamed content
    excluded_headers = ["content-encoding", "transfer-encoding", "content-length", "connection"]
    response_headers = [(name, value) for name, value in resp.raw.headers.items()
                        if name.lower() not in excluded_headers]

    return Response(resp.content, resp.status_code, response_headers)


if __name__ == "__main__":
    app.run(debug=True, port=5000)
