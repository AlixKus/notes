#JdbcTemplate
基本的SpringJDBC模板,封装了数据库相关操作，只写业务代码。

#获取
先配置数据源，才能获取`JdbcTemplate`
```
@Bean
public JdbcTemplate jdbcTemplate(DataSource dataSource)
{
	return new JdbcTemplate(dataSource);
}
```

#关键接口
##RowMapper<T>，重写mapRow方法，完成ResultSet到所需POJO的映射
```
@FunctionalInterface
public interface RowMapper<T> {
	@Nullable
	T mapRow(ResultSet rs, int rowNum) throws SQLException;

}
```
##原理
内部调用RowMapperResultSetExtractor<T>的extractData方法，完成了数据映射到List。
```
@Override
	public List<T> extractData(ResultSet rs) throws SQLException {
		List<T> results = (this.rowsExpected > 0 ? new ArrayList<>(this.rowsExpected) : new ArrayList<>());
		int rowNum = 0;
		while (rs.next()) {
			results.add(this.rowMapper.mapRow(rs, rowNum++));
		}
		return results;
	}
```

##命名参数
赋予SQL中每一个参数一个明确的名字，绑定值到改语句时通过名字引用参数。
```
	@Autowired
	NamedParameterJdbcTemplate namedParameterJdbcTemplate;
```
```
private static final String FIND_CATALOG_BY_NAME = "select * from Catalog where name = :name";

	@Override
	public Catalog findOneByName(String name) {
		Map<String, Object> params = new HashMap<>();
		params.put("name", name);
		return namedParameterJdbcTemplate.queryForObject(FIND_CATALOG_BY_NAME, params, new CatalogRowMapper());
	}
```

##实例
```
@Repository
public class CatalogRepositoryImp implements CatalogRepository {
	private static final String INSERT_CATALOG = "insert into Catalog (name) values (?)";

	private static final String FIND_ALL = "select * from Catalog";
	@Autowired
	JdbcTemplate jdbcTemplate;

	@Override
	public void addCatalog(Catalog catalog) {
		jdbcTemplate.update(INSERT_CATALOG, catalog.getName());
	}

	@Override
	public List<Catalog> findAll() {
		return jdbcTemplate.query(FIND_ALL, new CatalogRowMapper());
	}

	private static final class CatalogRowMapper implements RowMapper<Catalog> {
		@Override
		public Catalog mapRow(ResultSet rs, int rowNum) throws SQLException {
			Integer id = rs.getInt("id");
			String name = rs.getString("name");
			return new Catalog(id, name);
		}
	}
}
```

带返回值的插入
通过SimpleJdbcInsert API来完成
```
private long insertSpittleAndReturnId(Spittle spittle) {
			SimpleJdbcInsert jdbcInsert = new SimpleJdbcInsert(jdbcTemplate).withTableName("Spittle");
			jdbcInsert.setGeneratedKeyName("id");
			Map<String, Object> args = new HashMap<String, Object>();
			args.put("spitter", spittle.getSpitter().getId());
			args.put("message", spittle.getMessage());
			args.put("postedTime", spittle.getPostedTime());
			long spittleId = jdbcInsert.executeAndReturnKey(args).longValue();
			return spittleId;
	}
```
