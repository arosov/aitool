# AI Command-Line Assistant

This project provides a command-line interface (CLI) tool, `ai`, that interacts with a local Llamafile language model server. It allows users to send queries to the LLM and receive streaming markdown responses directly in their terminal.

## Features

*   **Direct LLM Interaction**: Send queries to a Llamafile-hosted language model.
*   **Streaming Output**: Responses are streamed back and rendered progressively as markdown.
*   **Rich Terminal Output**: Utilizes the `rich` library for formatted and readable markdown in the terminal, including syntax-highlighted code blocks.
*   **Systemd Integration**: Includes a `systemd` service file (`llamaserver.service`) to manage the Llamafile server process.
*   **Automatic Service Management**: The `ai` script can check if the `llamaserver.service` is running and attempt to start it if it's not.
*   **Command-line and Interactive Modes**:
    *   Pass a query directly as a command-line argument for a quick answer.
    *   Run without arguments to enter an interactive chat loop.
*   **Configurable Model**: The Llama model (`MODEL_NAME`) and server URL (`LLAMA_SERVER_URL`) can be configured within the `ai` script.

## Prerequisites

*   Python 3.x
*   The `requests` and `rich` Python libraries.
*   A Llamafile executable (e.g., `google_gemma-3-1b-it-Q6_K.llamafile`) and a compatible model.
*   `systemd` (if you want to use the provided service file).
*   `bash` (for the `ExecStart` command in the service file).

## Installation

1.  **Clone the repository (or download the files):**
    ```bash
    git clone <your-repository-url>
    cd <your-repository-directory>
    ```

2.  **Install Python dependencies:**
    ```bash
    pip install requests rich
    ```

3.  **Set up Llamafile:**
    *   Download your desired Llamafile (e.g., `google_gemma-3-1b-it-Q6_K.llamafile`) from the official Llamafile GitHub repository or other sources.
    *   Place the Llamafile executable in a directory, for example, `~/llamafiles/`.
    *   Make it executable: `chmod +x ~/llamafiles/google_gemma-3-1b-it-Q6_K.llamafile`.

4.  **Configure the `ai` script:**
    *   Open the `ai` script and ensure `LLAMA_SERVICE_NAME`, `LLAMA_SERVER_URL`, and `MODEL_NAME` are set according to your setup. By default, it's configured for `gemma3-1b-it-q6k` and `http://127.0.0.1:4141`.

5.  **Configure and install the systemd service (optional but recommended):**
    *   Edit `llamaserver.service`:
        *   Update `WorkingDirectory` to the directory where your Llamafile executable is located (e.g., `WorkingDirectory=%h/llamafiles/`).
        *   Update `ExecStart` to point to your Llamafile executable and desired model, port, and host. The current example is:
            ```
            ExecStart=/bin/bash -c './google_gemma-3-1b-it-Q6_K.llamafile --port 4141 --host 127.0.0.1 --server'
            ```
            Ensure the path to the llamafile is correct and it's executable.
    *   Copy the `llamaserver.service` file to your systemd user directory:
        ```bash
        mkdir -p ~/.config/systemd/user/
        cp llamaserver.service ~/.config/systemd/user/
        ```
    *   Reload the systemd user daemon:
        ```bash
        systemctl --user daemon-reload
        ```
    *   Enable the service to start on boot (optional):
        ```bash
        systemctl --user enable llamaserver.service
        ```
    *   Start the service:
        ```bash
        systemctl --user start llamaserver.service
        ```
    *   Check its status:
        ```bash
        systemctl --user status llamaserver.service
        ```

## Usage

Make sure the `ai` script is executable:
```bash
chmod +x ai
```

### Command-Line Query

To send a single query and get a response:
```bash
./ai "Your query here"
```
For example:
```bash
./ai "What is the command to list files in Linux?"
```

### Interactive Mode

To start an interactive chat session:
```bash
./ai
```
Then, type your queries at the `>` prompt. Type `exit` or `quit` (or press Ctrl+D) to end the session.

### Debug Mode

To enable verbose debugging output from the `ai` script, set `DEBUG_MODE = True` at the top of the `ai` file.

## Systemd Service Management

If you installed the `llamaserver.service`:

*   **Start the service:**
    ```bash
    systemctl --user start llamaserver.service
    ```
*   **Stop the service:**
    ```bash
    systemctl --user stop llamaserver.service
    ```
*   **Check the status:**
    ```bash
    systemctl --user status llamaserver.service
    ```
*   **View logs:**
    ```bash
    journalctl --user -u llamaserver.service
    ```
    (Add `-f` to follow logs in real-time)

The `ai` script will attempt to automatically start the service if it's not detected, provided the service file is correctly installed and configured.

## Contributing

Contributions are welcome! Please feel free to submit pull requests or open issues for bugs, feature requests, or improvements.

1.  Fork the repository.
2.  Create a new branch (`git checkout -b feature/your-feature-name`).
3.  Make your changes.
4.  Commit your changes (`git commit -am 'Add some feature'`).
5.  Push to the branch (`git push origin feature/your-feature-name`).
6.  Create a new Pull Request.

## License

This project is open-source. Please specify a license if you have one (e.g., MIT, Apache 2.0). If no license is specified, it's assumed to be under a default restrictive copyright.
Consider adding a `LICENSE` file to your repository.
