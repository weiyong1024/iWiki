# 并发编程

## 线程池

github上的开源线程池

* [progschj/ThreadPool](https://github.com/progschj/ThreadPool/blob/master/ThreadPool.h)
* [mtrebi/thread-pool](https://github.com/mtrebi/thread-pool)

### 线程数量固定的线程池

线程池可以拥有固定数量的工作线程（通常工作线程数量与 `std::thread::hardware_concurrency()` 相同）。

??? note "mtrebi/thread-pool 的线程池实现"
    ```cpp
    #include <bits/stdc++.h>

    // Thread safe implementation of a Queue using an std::queue
    template <typename T>
    class SafeQueue {
    public:
      SafeQueue() {}

      SafeQueue(SafeQueue& other) {
        // TODO:
      }

      ~SafeQueue() {}

      bool Empty() {
        std::unique_lock<std::mutex> lock(mutex_);
        return queue_.empty();
      }

      int Size() {
        std::unique_lock<std::mutex> lock(mutex_);
        return queue_.size();
      }

      void Enqueue(T& t) {
        std::unique_lock<std::mutex> lock(mutex_);
        queue_.push(t);
      }

      bool Dequeue(T& t) {
        std::unique_lock<std::mutex> lock(mutex_);

        if (queue_.empty()) {
          return false;
        }
        t = std::move(queue_.front());

        queue_.pop();
        return true;
      }

    private:
      std::queue<T> queue_;
      std::mutex mutex_;
    };

    class ThreadPool {
    public:
      ThreadPool(const int threads_num)
          : threads_(std::vector<std::thread>(threads_num)), shutdown_flag_(false) {}

      ThreadPool(const ThreadPool&) = delete;
      ThreadPool(ThreadPool&&) = delete;

      ThreadPool& operator=(const ThreadPool&) = delete;
      ThreadPool& operator=(ThreadPool&&) = delete;

      // Inits thread pool
      void Init() {
        for (int i = 0; i < threads_.size(); ++i) {
          threads_[i] = std::thread(ThreadWorker(this, i));
        }
      }

      // Waits until threads finish their current task and shutdowns the pool
      void Shutdown() {
        shutdown_flag_ = true;
        conditional_lock_.notify_all();

        for (int i = 0; i < threads_.size(); ++i) {
          if (threads_[i].joinable()) {
            threads_[i].join();
          }
        }
      }

      // Submit a function to be executed asynchronously by the pool
      template <typename F, typename... Args>
      auto Submit(F&& f, Args&&... args) -> std::future<decltype(f(args...))> {
        // Create a function with bounded parameters ready to execute
        std::function<decltype(f(args...))()> func =
            std::bind(std::forward<F>(f), std::forward<Args>(args)...);
        // Encapsulate it into a shared ptr in order to be able to copy construct /
        // assign
        auto task_ptr =
            std::make_shared<std::packaged_task<decltype(f(args...))()>>(func);

        // Wrap packaged task into void function
        std::function<void()> wrapper_func = [task_ptr]() { (*task_ptr)(); };

        // Enqueue generic wrapper function
        task_queue_.Enqueue(wrapper_func);

        // Wake up one thread if its waiting
        conditional_lock_.notify_one();

        // Return future from promise
        return task_ptr->get_future();
      }

    private:
      class ThreadWorker {
      public:
        ThreadWorker(ThreadPool* pool, const int id) : host_pool_(pool), m_id(id) {}

        void operator()() {
          std::function<void()> func;
          bool dequeued;
          while (!host_pool_->shutdown_flag_) {
            {
              std::unique_lock<std::mutex> lock(host_pool_->conditional_mutex_);
              if (host_pool_->task_queue_.Empty()) {
                host_pool_->conditional_lock_.wait(lock);
              }
              dequeued = host_pool_->task_queue_.Dequeue(func);
            }
            if (dequeued) {
              func();
            }
          }
        }

      private:
        int m_id;
        ThreadPool* host_pool_;
      };

      bool shutdown_flag_;
      SafeQueue<std::function<void()>> task_queue_;
      std::vector<std::thread> threads_;
      std::mutex conditional_mutex_;
      std::condition_variable conditional_lock_;
    };

    std::random_device rd;
    std::mt19937 mt(rd());
    std::uniform_int_distribution<int> dist(-1000, 1000);
    auto rnd = std::bind(dist, mt);


    void simulate_hard_computation() {
      std::this_thread::sleep_for(std::chrono::milliseconds(2000 + rnd()));
    }

    // Simple function that adds multiplies two numbers and prints the result
    void multiply(const int a, const int b) {
      simulate_hard_computation();
      const int res = a * b;
      std::cout << a << " * " << b << " = " << res << std::endl;
    }

    // Same as before but now we have an output parameter
    void multiply_output(int & out, const int a, const int b) {
      simulate_hard_computation();
      out = a * b;
      std::cout << a << " * " << b << " = " << out << std::endl;
    }

    // Same as before but now we have an output parameter
    int multiply_return(const int a, const int b) {
      simulate_hard_computation();
      const int res = a * b;
      std::cout << a << " * " << b << " = " << res << std::endl;
      return res;
    }


    void example() {
      // Create pool with 3 threads
      ThreadPool pool(3);

      // Initialize pool
      pool.Init();

      // Submit (partial) multiplication table
      for (int i = 1; i < 3; ++i) {
        for (int j = 1; j < 10; ++j) {
          pool.Submit(multiply, i, j);
        }
      }

      // Submit function with output parameter passed by ref
      int output_ref;
      auto future1 = pool.Submit(multiply_output, std::ref(output_ref), 5, 6);

      // Wait for multiplication output to finish
      future1.get();
      std::cout << "Last operation result is equals to " << output_ref << std::endl;

      // Submit function with return parameter 
      auto future2 = pool.Submit(multiply_return, 5, 3);

      // Wait for multiplication output to finish
      int res = future2.get();
      std::cout << "Last operation result is equals to " << res << std::endl;
      
      pool.Shutdown();
    }

    int main() {
      example();
    }
    ```

注意此处编译需要加上`-pthread`编译选项。

??? "测试结果"
    ```
    1 * 1 = 1
    1 * 2 = 2
    1 * 3 = 3
    1 * 6 = 6
    1 * 4 = 4
    1 * 5 = 5
    1 * 7 = 7
    1 * 8 = 8
    2 * 1 = 2
    1 * 9 = 9
    2 * 3 = 6
    2 * 2 = 4
    2 * 5 = 10
    2 * 4 = 8
    2 * 6 = 12
    2 * 7 = 14
    2 * 9 = 18
    2 * 8 = 16
    5 * 6 = 30
    Last operation result is equals to 30
    5 * 3 = 15
    Last operation result is equals to 15
    ```