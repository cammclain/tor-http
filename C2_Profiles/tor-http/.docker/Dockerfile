# Use the same builder stage as before
FROM golang:1.21 as builder

WORKDIR /Mythic/

COPY [".", "."]

RUN make build

# Start the final stage based on Alpine
FROM alpine

# Update apk repositories and Install make and Tor
RUN apk update && \
    apk add --no-cache make tor

# Setup Tor and the hidden service directory
RUN mkdir -p /var/lib/tor/hidden_service/ && \
    chown -R tor /var/lib/tor/hidden_service/
# You might need to adjust permissions depending on your setup

COPY --from=builder /main /main
COPY --from=builder /mythic_http_server /mythic_http_server

WORKDIR /Mythic/

COPY [".", "."]

# Add your custom torrc file to the container
COPY torrc /etc/tor/torrc

# Adjust CMD to start Tor in the background and then your service
# Note: Using `tor &` to start Tor in the background might not be the best approach in production.
# Consider using a process manager like Supervisor to manage multiple processes inside the Docker container.
CMD tor & make run
