## Script

```py
        self.counter = 0
        self.stats = defaultdict(list)
```

```py
        self.counter += 1
        if self.counter > 10:
            self.stats["lx"].append(rel_course_points[:, :, 0, 0])
            self.stats["ly"].append(rel_course_points[:, :, 0, 1])
            self.stats["lz"].append(rel_course_points[:, :, 0, 2])
            self.stats["cx"].append(rel_course_points[:, :, 1, 0])
            self.stats["cy"].append(rel_course_points[:, :, 1, 1])
            self.stats["cz"].append(rel_course_points[:, :, 1, 2])
            self.stats["rx"].append(rel_course_points[:, :, 2, 0])
            self.stats["ry"].append(rel_course_points[:, :, 2, 1])
            self.stats["rz"].append(rel_course_points[:, :, 2, 2])

        if self.counter >= 300:

            flattened_stats = dict()
            for key in self.stats.keys():
                flattened_stats[key] = np.concatenate([s.flatten() for s in self.stats[key]], axis=0)

            import matplotlib.pyplot as plt
            fig, axes = plt.subplots(3, 3, figsize=(12, 12))

            axes[0, 0].hist(flattened_stats["lx"], bins=100, color='blue', alpha=0.7)
            axes[0, 0].set_title('lx')
            axes[0, 1].hist(flattened_stats["ly"], bins=100, color='green', alpha=0.7)
            axes[0, 1].set_title('ly')
            axes[0, 2].hist(flattened_stats["lz"], bins=100, color='red', alpha=0.7)
            axes[0, 2].set_title('lz')
            axes[1, 0].hist(flattened_stats["cx"], bins=100, color='blue', alpha=0.7)
            axes[1, 0].set_title('cx')
            axes[1, 1].hist(flattened_stats["cy"], bins=100, color='green', alpha=0.7)
            axes[1, 1].set_title('cy')
            axes[1, 2].hist(flattened_stats["cz"], bins=100, color='red', alpha=0.7)
            axes[1, 2].set_title('cz')
            axes[2, 0].hist(flattened_stats["rx"], bins=100, color='blue', alpha=0.7)
            axes[2, 0].set_title('rx')
            axes[2, 1].hist(flattened_stats["ry"], bins=100, color='green', alpha=0.7)
            axes[2, 1].set_title('ry')
            axes[2, 2].hist(flattened_stats["rz"], bins=100, color='red', alpha=0.7)
            axes[2, 2].set_title('rz')

            plt.tight_layout()
            plt.show()
            exit()

```